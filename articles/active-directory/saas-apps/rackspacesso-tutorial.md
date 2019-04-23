---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Rackspace SSO-val |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Rackspace SSO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 36b398be-2f7e-4ce8-9031-53587299bc4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: fd420ea3fc4faae7fe4510a72204d71acaa3549a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009931"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Oktatóanyag: Az Azure Active Directory-integráció Rackspace SSO-val

Ebben az oktatóanyagban elsajátíthatja, hogyan Rackspace SSO integrálása az Azure Active Directory (Azure AD).
Rackspace SSO integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Rackspace egyszeri bejelentkezés az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett a Rackspace egyszeri bejelentkezés (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Rackspace SSO-val, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Rackspace egyszeri bejelentkezés az egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Rackspace SSO **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-rackspace-sso-from-the-gallery"></a>Rackspace SSO hozzáadása a katalógusból

Az Azure AD integrálása a Rackspace egyszeri bejelentkezés konfigurálásához hozzá kell Rackspace SSO a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Rackspace SSO a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Rackspace SSO**, jelölje be **Rackspace SSO** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában a Rackspace egyszeri bejelentkezés](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálni, és a Rackspace SSO-val az Azure AD egyszeri bejelentkezés tesztelése nevű tesztfelhasználó alapján **Britta Simon**.
A Rackspace használata egyszeri bejelentkezéshez, a Rackspace felhasználók automatikusan létrejön az első bejelentkezéskor a Rackspace-portálra. 

Az Azure AD egyszeri bejelentkezés a Rackspace egyszeri bejelentkezés tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Egyszeri bejelentkezés Rackspace egyszeri bejelentkezés konfigurálása](#configure-rackspace-sso-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Állítsa be a Rackspace Vezérlőpult attribútumleképzés](#set-up-attribute-mapping-in-the-rackspace-control-panel)**  – Rackspace szerepkörök hozzárendelése az Azure AD-felhasználók.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálásához a Rackspace SSO-val, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Rackspace SSO** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** részben, a feltöltési a **szolgáltató metaadatait tartalmazó fájl** , amely innen tölthető le: a [URL-cím](https://login.rackspace.com/federate/sp.xml) , és hajtsa végre az alábbi lépéseket:

    a. Kattintson a **metaadatfájl feltöltése**.

    ![image](common/upload-metadata.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![image](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltését követően a szükséges URL-cím lekérése automatikusan kitölti a rendszer automatikusan.

    d. Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe: `https://login.rackspace.com/federate/`

    ![Rackspace SSO tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-signonurl.png)   

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

Ez a fájl feltöltéséhez szükséges identitás-összevonási beállításait Rackspace feltöltés.

### <a name="configure-rackspace-sso-single-sign-on"></a>Rackspace SSO egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **Rackspace SSO** oldalán:

1. Lásd a dokumentációban a [Identitásszolgáltatóként a Vezérlőpult hozzáadása](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. Ez végigvezeti a lépéseket:
    1. Hozzon létre egy új identitásszolgáltató
    1. Adjon meg egy e-mail-tartományhoz, amely a felhasználói bejelentkezés a vállalat azonosítására.
    1. Töltse fel a **összevonási metaadatainak XML** korábban letöltött az Azure Vezérlőpultján.

Ez az Azure és a Rackspace való csatlakozáshoz szükséges alapszintű egyszeri bejelentkezési beállításainak megfelelően konfigurálja.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Rackspace SSO Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Rackspace SSO**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Rackspace SSO**.

    ![Az alkalmazások listáját a Rackspace egyszeri bejelentkezési hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Attribútumleképzés beállítása a Rackspace-Vezérlőpult

Használja a Rackspace- **attribútum leképezési házirend** Rackspace szerepkörök és csoportok hozzárendelése az egyszeri bejelentkezés felhasználók. A **attribútum leképezési házirend** Azure AD SAML jogcímek fordítja le a felhasználó konfigurációs mezők Rackspace igényel. További dokumentáció megtalálható a Rackspace [attribútum leképezési alapjai dokumentáció](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/). Néhány szempontot:

* Ha azt szeretné, különböző szintű Azure AD-csoportok használata a Rackspace-hozzáférés hozzárendelése, szüksége lesz ahhoz, hogy az Azure-ban a csoportok jogcím **Rackspace SSO** egyszeri bejelentkezés beállításai. A **attribútum leképezési házirend** ezután a rendszer használja ezeket a csoportokat a kívánt Rackspace szerepkörök és csoportok megfelelően:

    ![A csoportok jogcím-beállítások](common/sso-groups-claim.png)

* Alapértelmezés szerint az Azure AD SAML a jogcímek, az a csoport nevét és az egyedi azonosítója az Azure AD-csoportok küld. Azonban a helyszíni Active Directory, az Azure AD szolgáltatással szinkronizál, ha van a lehetőség használatával küldjön a tényleges nevét, a csoportokat:

    ![A csoportok jogcím-beállításai](common/sso-groups-claims-names.png)

Az alábbi példa **attribútum leképezési házirend** mutatja be:
1. Beállítás a Rackspace felhasználó nevét a `user.name` SAML-kérelmet. Minden olyan jogcímet is használható, de általános, hogy állítsa a bestattempt értékre a felhasználó e-mail-címét tartalmazó.
1. A Rackspace szerepkörök beállítása `admin` és `billing:admin` a felhasználó által az Azure AD-csoportok, vagy a csoport nevét, vagy a csoport egyedi azonosítója alapján egyeztetésével. A *behelyettesítési* , `"{0}"` a a `roles` mező szolgál, és lesz lecserélve az eredményeit a `remote` kifejezések szabály.
1. Használatával a `"{D}"` *behelyettesítési alapértelmezett* , hogy a Rackspace keres a szabványos és ismert az SAML exchange SAML-jogcímek mezőket SAML lekéréséhez.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Győződjön meg arról, hogy egy egyszerű szövegszerkesztőben, amely ellenőrzi a YAML-szintaxist a házirend-fájl szerkesztése során.

Tekintse meg a Rackspace [attribútum leképezési alapjai dokumentáció](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/) további példákat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Rackspace SSO csempére kattint, meg kell lehet automatikusan bejelentkezett a Rackspace SSO, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Is használhatja a **ellenőrzése** gombra a **Rackspace SSO** egyszeri bejelentkezés beállításai:

   ![SSO Validate Button](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

