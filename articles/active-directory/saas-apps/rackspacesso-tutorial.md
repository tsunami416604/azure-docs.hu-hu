---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Rackspace SSO-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Rackspace egyszeri bejelentkezés között.
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
ms.openlocfilehash: 98a160b361e316c87e61855825c2cc36fdbb06d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870303"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Oktatóanyag: Az Azure Active Directory integrációja a Rackspace SSO-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Rackspace SSO-t az Azure Active Directoryval (Azure AD).
A Rackspace SSO integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Rackspace SSO-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve rackspace egyszeri bejelentkezés (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció rackspace-sso-val való konfigurálásához a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Rackspace Egyszeri bejelentkezés egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Rackspace SSO támogatja **SP** kezdeményezett SSO

## <a name="adding-rackspace-sso-from-the-gallery"></a>Rackspace SSO hozzáadása a galériából

A Rackspace SSO azure-beli AD-be való integrálásának konfigurálásához hozzá kell adnia a rackspace-sso-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Rackspace SSO hozzáadásához a katalógusból hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Rackspace SSO**kifejezést , válassza a **Rackspace SSO** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Rackspace SSO az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Rackspace Egyszeri bejelentkezéssel egy **Britta Simon**nevű tesztfelhasználó alapján.
Ha egyszeri bejelentkezést használ a Rackspace-szel, a Rackspace-felhasználók automatikusan létrejönnek, amikor először jelentkeznek be a Rackspace portálra. 

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Rackspace egyszeri egyszeri bejelentkezéssel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Rackspace Egyszeri bejelentkezés](#configure-rackspace-sso-single-sign-on)** t - az egyszeri bejelentkezés i beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
1. **[Állítsa be az Attribútumleképezést a Rackspace Vezérlőpulton](#set-up-attribute-mapping-in-the-rackspace-control-panel)** – rackspace-szerepkörök hozzárendeléséhez az Azure AD-felhasználókhoz.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének rackspace egyszeri bejelentkezéssel való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Rackspace Egyszeri bejelentkezés** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban töltse fel a **szolgáltató metaadatfájlját,** amelyet az [URL-címről](https://login.rackspace.com/federate/sp.xml) tölthet le, és a következő lépéseket hajthatja végre:

    a. Kattintson **a Metaadatfájl feltöltése gombra.**

    ![image](common/upload-metadata.png)

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    ![image](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltése után a szükséges URL-ek automatikusan automatikusan kitöltődnek.

    d. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet:`https://login.rackspace.com/federate/`

    ![Rackspace Egyszeri bejelentkezés tartomány és URL-címek egyszeri bejelentkezési információk](common/sp-signonurl.png)   

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

Ez a fájl feltöltésre kerül a Rackspace-be a szükséges identitásösszevonási konfigurációs beállítások feltöltéséhez.

### <a name="configure-rackspace-sso-single-sign-on"></a>Rackspace egyszeri bejelentkezés konfigurálása

Egyszeri bejelentkezés konfigurálása a **Rackspace Egyszeri bejelentkezés** i.,

1. Tekintse meg az [Identitásszolgáltató hozzáadása a Vezérlőpulthoz](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/) című dokumentációt
1. Ez végigvezeti Önt a következő lépéseken:
    1. Új identitásszolgáltató létrehozása
    1. Adjon meg egy e-mail tartományt, amelyet a felhasználók a vállalat azonosítására használnak bejelentkezéskor.
    1. Töltse fel az **összevonási metaadat-XML-t,** amelyet korábban az Azure vezérlőpultjáról töltött le.

Ez helyesen konfigurálja az Azure és a Rackspace csatlakozásához szükséges alapvető SSO-beállításokat.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező `brittasimon@yourcompanydomain.extension`típusa mezőben. A BrittaSimon@contoso.com például

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon az Azure egyszeri bejelentkezést a Rackspace Egyszeri bejelentkezés engedélyezésével.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza **a Rackspace SSO**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Rackspace SSO**lehetőséget.

    ![A Rackspace SSO hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Attribútumleképezés beállítása a Rackspace vezérlőpulton

A Rackspace **egy attribútumleképezési szabályzatot** használ a Rackspace szerepkörök és csoportok egyszeri bejelentkezési felhasználókhoz rendelésére. Az **attribútumleképezési szabályzat** lefordítja az Azure AD SAML jogcímeket a Rackspace által igényelt felhasználói konfigurációs mezőkre. További dokumentáció a Rackspace [Attribute Mapping Basics dokumentációban](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)található. Néhány szempont:

* Ha az Azure AD-csoportok használatával a Rackspace-hozzáférés különböző szintjeit szeretné hozzárendelni, engedélyeznie kell a csoportok jogcímét az Azure **Rackspace egyszeri** bejelentkezési beállításaiban. Az **attribútumleképezési házirend** ezután a következő kretén csoportokat a kívánt Rackspace szerepkörökhöz és csoportokhoz igazítja:

    ![A Csoportok jogcímbeállításai](common/sso-groups-claim.png)

* Alapértelmezés szerint az Azure AD elküldi az Azure AD-csoportok UID-azonosítóját az SAML-jogcímben, szemben a csoport nevével. Ha azonban szinkronizálja a helyszíni Active Directoryt az Azure AD-vel, lehetősége van a csoportok tényleges nevének elküldésére:

    ![A Csoportok jogcímnév-beállításai](common/sso-groups-claims-names.png)

A következő példa **Az attribútumleképezési házirend** bemutatja:
1. A Rackspace-felhasználó nevének `user.name` beállítása AZ SAML jogcímre. Bármilyen jogcím használható, de a leggyakoribb, hogy ezt a mezőt a felhasználó e-mail címét tartalmazó mezőre állítják be.
1. A Rackspace-szerepkörök `admin` és `billing:admin` a felhasználó beállítása egy Azure AD-csoport egyeztetésével, a csoport neve vagy a Csoport UID szerint. A `roles` mező `"{0}"` *helyettesítése* használatos, és a `remote` szabálykifejezések eredményei helyettesítik.
1. Az `"{D}"` *alapértelmezett helyettesítés* használatával lehetővé teszi a Rackspace számára, hogy további SAML-mezőket szerezzen be szabványos és jól ismert SAML-jogcímek keresésével az SAML-cserében.

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
> Győződjön meg arról, hogy olyan szövegszerkesztőt használ, amely a házirendfájl szerkesztésekor érvényesíti a YAML szintaxisát.

További példákat a Rackspace [Attribútumleképezés alapjai dokumentációban](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) talál.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Rackspace SSO csempére kattint, automatikusan be kell jelentkeznie a Rackspace SSO-ba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Az **Érvényesítés** gombot a **Rackspace Egyszeri** bejelentkezés iszaszó beállításaiban is használhatja:

   ![SSO-érvényesítés gomb](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

