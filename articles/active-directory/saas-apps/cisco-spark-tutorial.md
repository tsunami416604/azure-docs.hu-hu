---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Cisco Webex |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Cisco Webex között.
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
ms.devlang: na
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dc537a631cd083da0f902fb4fcd44d47756eeba
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471770"
---
# <a name="tutorial-integrate-cisco-webex-with-azure-active-directory"></a>Oktatóanyag: Cisco Webex integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a Cisco Webex integrálása az Azure Active Directory (Azure AD) lesz. Cisco Webex integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá a Cisco Webex Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve Cisco Webex az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Cisco Webex egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Cisco Webex támogatja **SP és IDP** által kezdeményezett egyszeri Bejelentkezést, és támogatja a **automatikus** felhasználóátadást készíthet elő.

## <a name="adding-cisco-webex-from-the-gallery"></a>Cisco Webex hozzáadása a katalógusból

Az Azure AD integrálása a Cisco Webex konfigurálásához hozzá kell Cisco Webex a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Cisco Webex** kifejezést a keresőmezőbe.
1. Válassza ki **Cisco Webex** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Cisco Webex nevű tesztfelhasználó használata az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása **B.Simon**. Működik az egyszeri bejelentkezés Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolat létrehozására a Cisco Webex szüksége.

Az Azure AD SSO Cisco Webex tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Cisco Webex konfigurálása](#configure-cisco-webex)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az B.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  B.Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre a Cisco Webex tesztfelhasználót](#create-cisco-webex-test-user)**  van egy B.Simon megfelelője a Cisco Webex, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Cisco Webex** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszt, és töltse fel a letöltött **szolgáltató metaadatok** fájlt, és az alkalmazás konfigurálása a következő lépések végrehajtásával:

    >[!Note]
    >A Service Provider metaadatok fájlt kap a **Cisco Webex** portálon. 

    a. Kattintson a **metaadatfájl feltöltése**.

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    c. Szolgáltató metaadatfájl feltöltése sikeres befejezése után a **azonosító** és **válasz URL-cím** értékeket automatikusan az első **alapszintű SAML-konfigurációja** a szakaszban:

    A a **bejelentkezési URL-cím** szövegmezőjébe illessze be az értéket, **válasz URL-cím**, amely lekérdezi autofilled SP a metaadat-fájl feltöltése.

5. Cisco Webex-alkalmazásban a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva adja hozzá az attribútumokat.

    ![image](common/edit-attribute.png)

6. Emellett a fent Cisco Webex-alkalmazásban vár néhány további attribútumok vissza SAML-válasz átadni. A a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen a következő lépésekkel adja hozzá a SAML-jogkivonat attribútumot, ahogyan az alábbi táblázatban:
    
    | Name (Név) |  Adatforrás-attribútum|
    | ---------------|--------- |
    | egyedi azonosítója | user.userprincipalname |

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

1. Az a **beállításához a Cisco Webex** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-cisco-webex"></a>Cisco Webex konfigurálása

1. Jelentkezzen be a [Cisco együttműködési Felhőfelügyelet](https://admin.ciscospark.com/) a teljes körű rendszergazdai hitelesítő adataival.

2. Válassza ki **beállítások** és a **hitelesítési** területén kattintson **módosítás**.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
  
3. Válassza ki **integrálása egy 3. fél identitásszolgáltatót. (Speciális)**  , és folytassa a következő képernyőn.

4. Az a **Idp-metaadatok importálása** oldalon, vagy húzza át, és dobja el az Azure ad-ben metaadatait tartalmazó fájl a lapra, vagy a fájl a böngésző lehetőséget használva keresse meg és az Azure AD-metaadatfájl feltöltésére. Ezután válassza ki **szükséges metaadatokat (biztonságosabb) a hitelesítésszolgáltató által aláírt tanúsítvány** kattintson **tovább**.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

5. Válassza ki **SSO-kapcsolat tesztelése**, és a egy új böngészőlapon nyílik meg, amikor hitelesítéshez az Azure AD-bejelentkezés.

6. Lépjen vissza a **Cisco együttműködési Felhőfelügyelet** böngészőlapon. Ha a teszt sikeres volt, válassza ki a **a teszt sikeres volt. Egyszeri bejelentkezés a beállítás engedélyezése** kattintson **tovább**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon B.Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban B.Simon által biztosított hozzáférés Cisco Webex Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Cisco Webex**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **B.Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-cisco-webex-test-user"></a>Cisco Webex tesztfelhasználó létrehozása

Ebben a szakaszban egy Cisco Webex Britta Simon nevű felhasználói hoz létre. Ebben a szakaszban egy Cisco Webex Britta Simon nevű felhasználói hoz létre.

1. Nyissa meg a [Cisco együttműködési Felhőfelügyelet](https://admin.ciscospark.com/) a teljes körű rendszergazdai hitelesítő adataival.

2. Kattintson a **felhasználók** , majd **felhasználók kezelése**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. Az a **kezelése felhasználó** ablakban válassza **manuális hozzáadása, vagy módosítsa a felhasználók** kattintson **tovább**.

4. Válassza ki **nevek és E-mail-cím**. Ezután adja meg a szövegmezőben, hogy a következő:

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    a. Az a **Utónév** szövegmező, például a felhasználó utóneve típus **B**.

    b. Az a **Vezetéknév** szövegmezőbe írja be például a felhasználó vezetékneve **Simon**.

    c. Az a **E-mail-cím** szövegmezőbe felhasználó e-mail címét írja be például a b.simon@contoso.com.

5. Kattintson a plusz jelre Britta Simon hozzáadásához. Ezután kattintson a **Tovább** gombra.

6. Az a **szolgáltatás hozzáadása a felhasználók számára** ablakban kattintson a **mentése** , majd **Befejezés**.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A Cisco Webex csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a Cisco Webex, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)