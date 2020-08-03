---
title: 'Oktatóanyag: Azure Active Directory integráció a Freshdeskbe-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Freshdeskbe között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b7188708c44d00d075a02d54b0a3b10cbc81954
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513534"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Oktatóanyag: Azure Active Directory integráció a Freshdeskbe

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Freshdeskbe a Azure Active Directory (Azure AD) szolgáltatással.
A Freshdeskbe és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Freshdeskbe.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Freshdeskbe (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Freshdeskbe való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Freshdeskbe egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Freshdeskbe támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A Freshdeskbe konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-freshdesk-from-the-gallery"></a>Freshdeskbe hozzáadása a gyűjteményből

A Freshdeskbe Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Freshdeskbe a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **freshdeskbe** kifejezést a keresőmezőbe.
1. Válassza ki a **freshdeskbe** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshdesk"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Freshdeskbe

Konfigurálja és tesztelje az Azure AD SSO-t a Freshdeskbe a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Freshdeskbe-ben.

Az Azure AD SSO és a Freshdeskbe konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
    1. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
1. **[Freshdeskbe egyszeri bejelentkezés konfigurálása](#configure-freshdesk-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre freshdeskbe-teszt felhasználót](#create-freshdesk-test-user)** – hogy a freshdeskbe Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

1. A [Azure Portal](https://portal.azure.com/) **freshdeskbe** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő mintával: `https://<tenant-name>.freshdesk.com` vagy bármely más, a freshdeskbe által javasolt érték.

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő mintával: `https://<tenant-name>.freshdesk.com` vagy bármely más, a freshdeskbe által javasolt érték.

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez forduljon a Freshdeskbe ügyfélszolgálati [csapatához](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Freshdeskbe alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, míg az **egyedi felhasználói azonosító** a **User. userPrincipalName** van leképezve, de a freshdeskbe azt várja, hogy a jogcím a **User. mail**használatával legyen leképezve, ezért az attribútum-hozzárendelés szerkesztéséhez kattintson a Szerkesztés ikonra, és módosítsa az attribútumok leképezését.

    ![image](common/edit-attribute.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Freshdeskbe beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon \@ yourcompanydomain. Extension** nevet  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** lehetőségre.


### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Freshdeskbe hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **freshdeskbe**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza a **freshdeskbe**lehetőséget.

    ![Az Freshdeskbe hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-freshdesk-single-sign-on"></a>Freshdeskbe egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Freshdeskbe vállalati webhelyre rendszergazdaként.

2. Válassza a **Biztonság ikont** , és a **Biztonság** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés](./media/freshdesk-tutorial/configure-1.png "Egyszeri bejelentkezés")
  
    a. Az **egyszeri bejelentkezéshez**válassza **a be**lehetőséget.

    b. A **login metódusban**válassza az **SAML egyszeri bejelentkezés**lehetőséget.

    c. Az identitásszolgáltató szövegmezőben **megadott entitás-azonosítóban** illessze be az **entitás-azonosító** értékét, amelyet a Azure Portal másolt.

    d. Az **SAML SSO URL** szövegmezőbe illessze be a **bejelentkezési URL** értékét, amelyet a Azure Portal másolt.

    e. Az **aláírási beállítások**területen jelölje be a **csak az aláírt érvényesítések** lehetőséget a legördülő listából.

    f. A **kijelentkezési URL** szövegmezőben illessze be a **kijelentkezési URL-** értéket, amelyet a Azure Portal másolt.

    : A **biztonsági tanúsítvány** szövegmezőbe illessze be a korábban beszerzett **tanúsítvány (Base64)** értékét.
  
    h. Kattintson a **Mentés** gombra.

## <a name="create-freshdesk-test-user"></a>Freshdeskbe-tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Freshdeskbe, a Freshdeskbe-ben kell kiépíteni őket.  
Freshdeskbe esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **freshdeskbe** -bérlőbe.

1. A bal oldali menüben kattintson a **rendszergazda** elemre, majd az **általános beállítások** lapon kattintson az **ügynökök**elemre.
  
    ![Ügynökök](./media/freshdesk-tutorial/create-user-1.png "Ügynökök")

1. Kattintson az **új ügynök**elemre.

    ![Új ügynök](./media/freshdesk-tutorial/create-user-2.png "Új ügynök")

1. Az ügynök adatai párbeszédpanelen adja meg a szükséges mezőket, és kattintson az **ügynök létrehozása**lehetőségre.

    ![Ügynök adatai](./media/freshdesk-tutorial/create-user-3.png "Ügynök adatai")

    >[!NOTE]
    >Az Azure AD-fiók tulajdonosa egy e-mailt fog kapni, amely tartalmaz egy hivatkozást, amely a fiók aktiválását megelőzően megerősíti a fiókot.
    >
    >[!NOTE]
    >A Freshdeskbe által biztosított bármely más Freshdeskbe felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat az Azure AD felhasználói fiókjainak Freshdeskbe való kiépítéséhez.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Freshdeskbe csempére kattint, automatikusan be kell jelentkeznie arra a Freshdeskbe, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

