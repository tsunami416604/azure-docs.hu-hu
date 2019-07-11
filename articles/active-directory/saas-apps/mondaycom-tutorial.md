---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező monday.com |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és monday.com között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93c5e473c62dc6b38f0b2c2906560d6099842d49
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718498"
---
# <a name="tutorial-integrate-mondaycom-with-azure-active-directory"></a>Oktatóanyag: Monday.com integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a monday.com integrálása az Azure Active Directory (Azure AD) lesz. Monday.com integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá monday.com Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve az Azure AD-fiókjukat monday.com.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Monday.com egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Monday.com támogatja **SP és IDP** által kezdeményezett egyszeri Bejelentkezést, és támogatja a **igény szerinti** felhasználóátadást készíthet elő.

## <a name="adding-mondaycom-from-the-gallery"></a>Monday.com hozzáadása a katalógusból

Az Azure AD integrálása a monday.com konfigurálásához hozzá kell monday.com a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **monday.com** kifejezést a keresőmezőbe.
1. Válassza ki **monday.com** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata monday.com **B.Simon**. Az SSO működjön kell monday.com az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Konfigurálás, és tesztelés az Azure AD SSO monday.com, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja a monday.com](#configure-mondaycom)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az B.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  B.Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre monday.com tesztfelhasználót](#create-mondaycom-test-user)**  B.Simon egy megfelelője a felhasználó Azure ad-ben reprezentációja kapcsolódó monday.com a rendelkeznie.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **monday.com** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** ablaktáblán, ha a service provider metaadatait tartalmazó fájl rendelkezik, és a konfigurálni kívánt **Identitásszolgáltató által kezdeményezett** mód, hajtsa végre az alábbi lépéseket:

    1. Válassza ki **metaadatfájl feltöltése**.

    1. Válassza ki a metaadatait tartalmazó fájl, a mappa ikonra, és válassza **feltöltése**.

    1. A metaadatait tartalmazó fájl sikeresen feltöltve, miután a **azonosító** és **válasz URL-cím** értékeket automatikusan fel van töltve a a **alapszintű SAML-konfigurációja** panelen:

       > [!Note]
       > Ha a **azonosító** és **válasz URL-cím** értékek nem get kitölti a rendszer automatikusan, majd töltse ki az értékeket manuálisan. A **azonosító** és a **válasz URL-cím** azonos és érték szerepel a következő mintának: `https://<your-domain>.monday.com/saml/saml_callback`

1. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<your-domain>.monday.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítója, válasz URL-cím és a bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [monday.com ügyfél-támogatási csapatának](mailto:dev@food.ee) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. Monday.com alkalmazását a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a felhasználói attribútumok párbeszédpanel.

    ![A felhasználói attribútumok ablaktáblából.](common/edit-attribute.png)

1. Emellett a fentiekben monday.com alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. A a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen a következő lépésekkel adja hozzá a SAML-jogkivonat attribútumot, ahogyan az alábbi táblázatban:

    | Name (Név) | Adatforrás-attribútum|
    | ---------------| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Távolítsa el a **Namespace**.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson az **OK** gombra.

    g. Kattintson a **Save** (Mentés) gombra.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **tanúsítvány (Base64)** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. Az a **monday.com beállítása** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-mondaycom"></a>Monday.com konfigurálása

1. Automatizálhatja a monday.com konfigurációra, telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![Saját alkalmazások kiterjesztése](common/install-myappssecure-extension.png)

1. A felvett bővítmény a böngészőre, kattintson a **monday.com beállítása** amely lesz az monday.com alkalmazáshoz irányítja. Itt adja meg a rendszergazdai hitelesítő adataival bejelentkezni monday.com. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és 3 – 6. lépések automatizálásához.

    ![Konfiguráció beállítása](common/setup-sso.png)

1. Szeretné monday.com manuálisan telepíteni, ha nyisson meg egy új böngészőablakban, és jelentkezzen be rendszergazdaként monday.com, és hajtsa végre az alábbi lépéseket:

1. Nyissa meg a **profil** lapot, majd kattintson a jobb felső sarokban lévő **rendszergazdai**.

    ![Monday.com konfiguráció](./media/mondaycom-tutorial/configuration01.png)

1. Válassza ki **biztonsági** , és győződjön meg arról, hogy kattintson a **nyílt** SAML mellett.

    ![Monday.com konfiguráció](./media/mondaycom-tutorial/configuration02.png)

1. Adja meg az identitásszolgáltató részleteit alább olvashatja.

    ![Monday.com konfiguráció](./media/mondaycom-tutorial/configuration03.png)

    >[!NOTE]
    >További részletekért tekintse meg [ez](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642) cikk

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

Ebben a szakaszban B.Simon használja az Azure egyszeri bejelentkezés monday.com való hozzáférést kell engedélyeznie.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **monday.com**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **B.Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-mondaycom-test-user"></a>Monday.com tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználó monday.com jön létre. Monday.com támogatja a just-in-time-kiépítés, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó már nem létezik az monday.com, amikor megpróbálja elérni monday.com egy új jön létre.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A monday.com csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a monday.com, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
