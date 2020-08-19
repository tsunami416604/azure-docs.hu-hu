---
title: 'Oktatóanyag: Azure Active Directory integráció a JIRA SAML SSO-val a Microsofttal (V 5.2) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és JIRA SAML egyszeri bejelentkezés között a Microsoft (V 5.2) használatával.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: e0198fdcfea1656e3aec5179358e69fb6fb55723
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547532"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Oktatóanyag: Azure Active Directory integráció a JIRA SAML SSO-val a Microsofttal (V 5.2)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a JIRA SAML SSO-t a Microsoft (V 5.2) használatával a Azure Active Directory (Azure AD) segítségével.
A JIRA SAML SSO Microsoft (V 5.2) és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy a Microsoft (V 5.2) Hogyan férhet hozzá az SAML SSO-JIRA.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az SAML SSO JIRA a Microsoft (V 5.2) (egyszeri bejelentkezés) Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="description"></a>Description

Az egyszeri bejelentkezés engedélyezéséhez használja a Microsoft Azure Active Directory-fiókját a Atlassian JIRA-kiszolgálóval. Így az összes céges felhasználó használhatja az Azure AD hitelesítő adatait a JIRA alkalmazásba való bejelentkezéshez. Ez a beépülő modul SAML 2,0-et használ a összevonáshoz.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a JIRA SAML SSO használatával a Microsoftnál (V 5.2) a következő elemek szükségesek:

- Azure AD-előfizetés
- A JIRA Core és a 5,2 szoftvernek telepítve és konfigurálva kell lennie a Windows 64 bites verzióján
- A JIRA-kiszolgáló HTTPS-kompatibilis
- Vegye figyelembe, hogy a JIRA beépülő modul támogatott verziói az alábbi szakaszban vannak felsorolva.
- A JIRA-kiszolgáló elérhető az interneten, különösen az Azure AD bejelentkezési oldalán a hitelesítéshez, és képesnek kell lennie fogadni a jogkivonatot az Azure AD-től.
- A rendszergazdai hitelesítő adatok beállítása a JIRA
- A websudo le van tiltva a JIRA
- A JIRA-kiszolgáló alkalmazásban létrehozott felhasználó tesztelése

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem ajánlott a JIRA éles környezetének használata. Először tesztelje az integrációt az alkalmazás fejlesztési vagy előkészítési környezetében, majd használja az éles környezetet.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi javaslatokat:

- Ne használja éles környezetét, ha szükséges.
- Ha nem rendelkezik Azure AD-próbaverzióval, egy hónapos próbaverziót is beszerezhet itt: [próbaverziós ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>A JIRA támogatott verziói

* JIRA Core és szoftver: 5,2
* A JIRA a 6,0-7,12 is támogatja. További részletekért kattintson a [Microsoft SAML SSO JIRA](jiramicrosoft-tutorial.md)

> [!NOTE]
> Vegye figyelembe, hogy a JIRA beépülő modul az Ubuntu 16,04-es verzióján is működik

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* JIRA SAML SSO a Microsofttól (V 5.2) támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>JIRA SAML SSO hozzáadása a Microsofttól (V 5.2) a katalógusból

Ahhoz, hogy a JIRA SAML SSO-t a Microsoft (V 5.2) használatával konfigurálja az Azure AD-be, hozzá kell adnia a JIRA SAML SSO-t a Microsoft (V 5.2) szolgáltatásból a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A következő lépésekkel adhat hozzá JIRA SAML SSO-t a Microsofttól (V 5.2) a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **JIRA SAML SSO a Microsoft számára (v 5.2)**, válassza a **JIRA SAML SSO a Microsofttól (v 5.2)** az eredmény panelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![JIRA SAML SSO a Microsofttól (V 5.2) az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a JIRA SAML SSO-val a Microsoft (V 5.2) alapján, egy **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolati kapcsolatra van szükség a Microsoft által használt SAML SSO JIRA (V 5.2).

Az Azure AD egyszeri bejelentkezés konfigurálásához és teszteléséhez a Microsoft által létrehozott SAML SSO-JIRA (V 5.2) a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[JIRA SAML SSO konfigurálása a Microsoft (v 5.2) egyszeri bejelentkezéssel](#configure-jira-saml-sso-by-microsoft-v52-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre JIRA SAML SSO-t a Microsoft (v 5.2) használatával](#create-jira-saml-sso-by-microsoft-v52-test-user)** , hogy a Microsoft (v 5.2) a felhasználó Azure ad-képviseletéhez kapcsolódó Britta Simon partnere legyen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezésének konfigurálása a JIRA SAML SSO használatával a Microsofttól (V 5.2) hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **JIRA SAML SSO a Microsoft (v 5.2)** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![JIRA SAML SSO a Microsoft (V 5.2) tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával: `https://<domain:port>/`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. A port nem kötelező, ha az egy elnevezett URL-cím. Ezek az értékek a JIRA beépülő modul konfigurálása során érkeznek, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-v52-single-sign-on"></a>JIRA SAML SSO konfigurálása a Microsoft (V 5.2) egyszeri bejelentkezéssel

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a JIRA-példányba.

2. Mutasson a fogaskerékre, és kattintson a **bővítmények**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon1.png)

3. A Bővítmények lap beállítások területén kattintson a **Bővítmények kezelése**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon7.png)

4. Töltse le a beépülő modult a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=56521). Töltse fel manuálisan a Microsoft által biztosított beépülő modult a **feltöltés** menü használatával. A beépülő modul letöltése a [Microsoft szolgáltatási szerződése](https://www.microsoft.com/servicesagreement/)alá tartozik.

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon12.png)

5. A beépülő modul telepítése után a **felhasználó által telepített** bővítmények szakaszban jelenik meg. Az új beépülő modul konfigurálásához kattintson a **Konfigurálás** elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon13.png)

6. Hajtsa végre a következő lépéseket a konfiguráció lapon:

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Győződjön meg arról, hogy az alkalmazáshoz csak egy tanúsítvány van hozzárendelve, így nincs hiba a metaadatok feloldásakor. Ha több tanúsítvány is van, a metaadatok feloldásakor a rendszergazda hibaüzenetet kap.

    a. A **metaadatok URL-címe** szövegmezőbe illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portal másolt, majd kattintson a **feloldás** gombra. Beolvassa a identitásszolgáltató metaadat-URL-címét, és feltölti az összes mező információit.

    b. Másolja ki az **azonosítót, a válasz URL-címét és a bejelentkezési URL** -értékeket, majd illessze be őket az azonosító, a válasz URL-címére, **majd jelentkezzen be az URL-** címek szövegmezőbe a Azure Portal **alapszintű SAML konfigurációs**

    c. A **bejelentkezési gomb neve** mezőbe írja be annak a gombnak a nevét, amelyet a szervezet a felhasználók számára látni kíván a bejelentkezési képernyőn.

    d. Az **SAML felhasználói azonosító helyein** válassza **a felhasználói azonosító elemet a tulajdonos utasítás NameIdentifier elemében** , vagy a **felhasználói azonosító egy attribútum elemben**.  Ennek az AZONOSÍTÓnak a JIRA felhasználói AZONOSÍTÓnak kell lennie. Ha a felhasználói azonosító nem egyezik, a rendszer nem engedélyezi a felhasználók számára a bejelentkezést.

    > [!Note]
    > Az SAML alapértelmezett felhasználói AZONOSÍTÓjának helye a név azonosítója. Ezt módosíthatja egy attribútum lehetőségre, és megadhatja a megfelelő attribútum nevét.

    e. Ha a **felhasználói azonosító** elemet választja, akkor az attribútum **neve** szövegmezőbe írja be annak az attribútumnak a nevét, ahol a rendszer a felhasználói azonosítót várta. 

    f. Ha az összevont tartományt (például az ADFS-t stb.) használja az Azure AD-vel, kattintson a **Kezdőlap tartomány felderítésének engedélyezése** lehetőségre, és konfigurálja a **tartománynevet**.

    : A **tartománynév** mezőben adja meg a tartománynevet az ADFS-alapú bejelentkezés esetén.

    h. Jelölje be az egyszeri Kijelentkezés **engedélyezése** lehetőséget, ha ki szeretne jelentkezni az Azure ad-ből, amikor egy felhasználó kijelentkezik a JIRA. 

    i. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

    > [!NOTE]
    > A telepítéssel és a hibaelhárítással kapcsolatos további információkért tekintse meg az [MS JIRA SSO-összekötő rendszergazdai útmutatóját](../ms-confluence-jira-plugin-adminguide.md) , valamint a segítségére vonatkozó [gyakori kérdéseket](../ms-confluence-jira-plugin-faq.md) is.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **nevet** `brittasimon\@yourcompanydomain.extension` . Például: BrittaSimon@contoso.com.

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson a Microsoft (V 5.2) JIRA SAML SSO-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **JIRA SAML SSO elemet a Microsofttól (v 5.2)**.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **JIRA SAML SSO a Microsofttól (v 5.2)** lehetőséget.

    ![A JIRA SAML SSO a Microsoft (V 5.2) hivatkozással az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>JIRA SAML SSO létrehozása a Microsofttól (V 5.2) tesztelési felhasználó

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a helyszíni JIRA, a helyszíni JIRA kell kiépíteni őket.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a JIRA a helyszíni kiszolgálóra rendszergazdaként.

2. Mutasson a fogaskerékre, és kattintson a **felhasználó-felügyelet**elemre.

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user1.png)

3. A rendszer átirányítja a rendszergazdai hozzáférés lapra a **jelszó** megadásához, majd kattintson a **megerősítés** gombra.

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user2.png)

4. A **felhasználói kezelés** lap szakaszban kattintson a **felhasználó létrehozása**elemre.

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user3.png) 

5. Az **"új felhasználó létrehozása"** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user4.png)

    a. Az **e-mail cím** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .

    b. A **teljes név** szövegmezőbe írja be a felhasználó teljes nevét, például a Britta Simon nevet.

    c. A **Felhasználónév** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .

    d. A **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    e. Kattintson a **felhasználó létrehozása**gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a JIRA SAML SSO-t a Microsoft (V 5.2) csempére kattint a hozzáférési panelen, automatikusan be kell jelentkeznie a JIRA SAML SSO-ba a Microsoft (V 5.2) használatával, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
