---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az eltéréseket |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az eltéréseket között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 39dcbb95-c192-448c-86a1-cedede1c0972
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4728ad4fcd44c754a62ec19037562e63d92ec304
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656613"
---
# <a name="tutorial-integrate-drift-with-azure-active-directory"></a>Oktatóanyag: Eltéréseket integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a eltéréseket integrálása az Azure Active Directory (Azure AD) lesz. Eltolódás integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá az eltéréseket az Azure AD-ben.
* Engedélyezze a felhasználókat, hogy a rendszer automatikusan bejelentkezve eltéréseket az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Eltolódás egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja az eltérő **SP és IDP** által kezdeményezett egyszeri bejelentkezés és **igény szerinti** felhasználóátadást készíthet elő.

## <a name="adding-drift-from-the-gallery"></a>Eltolódás hozzáadása a katalógusból

Eltolódás az Azure AD-be, az integráció konfigurálásához hozzá kell eltéréseket a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **eltéréseket** kifejezést a keresőmezőbe.
1. Válassza ki **eltéréseket** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata eltéréseket **b Simon**. SSO működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolatot hozhat létre az eltéréseket az kell.

Az Azure AD SSO eltéréseket tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja az eltéréseket](#configure-drift)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés a b Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  b Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre eltéréseket tesztfelhasználót](#create-drift-test-user)**  egy megfelelője a b Simon van, amely kapcsolódik az Azure AD felhasználói ábrázolása eltéréseket.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **eltéréseket** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** szakaszban az alkalmazás előre konfigurálva, és a szükséges URL-címek vannak már előre fel van töltve az Azure-ral. A felhasználónak szüksége van a konfiguráció mentéséhez kattintson a **mentése** gombra, és hajtsa végre az alábbi lépéseket:

    a. Kattintson a **további URL-címet beállítani**.
 
    b. Az a **továbbítási állapot** szövegmezőbe írja be egy URL-címe: `https://app.drift.com` 

    c. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett módban hajtsa végre a következő lépést:

    d. Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe: `https://start.drift.com`

6. Eltolódás alkalmazását a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a felhasználói attribútumok párbeszédpanel.

    ![image](common/edit-attribute.png)

7. Emellett a fenti Konfigurációsodródás alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. A felhasználói jogcímek szakaszban felhasználói attribútumok párbeszédpanelen a következő lépésekkel SAML-jogkivonat attribútum hozzáadása, ahogyan az az alábbi táblázatban: 

    | Name (Név) | Adatforrás-attribútum|
    | ---------------| --------------- |    
    | Name (Név) | user.displayname |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **összevonási metaadatainak XML** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. Az a **eltéréseket beállítása** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-drift"></a>Eltolódás konfigurálása

1. Automatizálhatja a konfigurációs eltéréseket belül, telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![Saját alkalmazások kiterjesztése](common/install-myappssecure-extension.png)

2. A felvett bővítmény a böngészőre, kattintson a **telepítő eltéréseket** átirányítja azt az eltéréseket alkalmazást. Itt adja meg a rendszergazdai hitelesítő adataival bejelentkezni az eltéréseket. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és 3-4 lépések automatizálásához.

    ![Konfiguráció beállítása](common/setup-sso.png)

3. Ha szeretné manuálisan beállítani az eltéréseket, nyisson meg egy új böngészőablakban, és jelentkezzen be rendszergazdaként vállalati eltéréseket webhelyét, és hajtsa végre az alábbi lépéseket:

4. A menüsávon a bal oldali menüjében kattintson a **beállítások ikon** > **Alkalmazásbeállítások** > **hitelesítési** , és hajtsa végre az alábbi lépéseket:

    ![A rendszergazda hivatkozás](./media/drift-tutorial/tutorial_drift_admin.png)

    a. Töltse fel a **összevonási metaadatainak XML** be az Azure Portalról letöltött a **metaadatfájl feltöltése identitásszolgáltató** szövegmezőben.

    b. A metaadat-fájl feltöltése után a többi érték első automatikus automatikusan kitölti az oldalon.

    c. Kattintson a **SAML engedélyezése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon b Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B. Simon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a hozzáférés biztosításával az eltéréseket Azure egyszeri bejelentkezés használatára b Simon engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **eltéréseket**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **b Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-drift-test-user"></a>Eltolódás tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre az eltéréseket. Eltolódás támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó már nem létezik az eltéréseket, egy új jön létre a hitelesítés után.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [eltéréseket támogatási csapatának](mailto:integrations@drift.com).

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Az eltéréseket csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett, amelynek beállítása egyszeri bejelentkezés az eltéréseket. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)