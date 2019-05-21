---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező monday.com |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és monday.com között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc142bf02a44ea85861f4cc648fd7ee8602c7520
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65896875"
---
# <a name="tutorial-azure-active-directory-integration-with-mondaycom"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező monday.com

Ebben az oktatóanyagban elsajátíthatja, hogyan monday.com integrálása az Azure Active Directory (Azure AD).

Monday.com integrálása az Azure AD kínál fel a következő előnyökkel jár:

* Az Azure AD, hogy ki férhet hozzá monday.com is használhatja.
* Felhasználók is automatikusan bejelentkeznie az Azure ad-ben fiókjukkal (egyszeri bejelentkezés) monday.com.
* A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

Az Azure ad-vel szoftverként (saas biztosított) alkalmazás integrációja szoftverrel kapcsolatos további információkért lásd: [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Monday.com az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik Azure AD előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.
* Az egyszeri bejelentkezés engedélyezve monday.com előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálni és a egy tesztkörnyezetben az Azure AD egyszeri bejelentkezés tesztelése és monday.com integrálása az Azure ad-ben.

Monday.com támogatja a következő funkciókat:

* **SP által kezdeményezett egyszeri bejelentkezés**
* **Identitásszolgáltató által kezdeményezett egyszeri bejelentkezés**
* **Felhasználókiépítés igény**

## <a name="add-mondaycom-in-the-azure-portal"></a>Az Azure Portalon monday.com hozzáadása

Monday.com integrálható az Azure ad-vel, hozzá kell adnia monday.com a felügyelt SaaS-alkalmazások listájában.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directoryval opciót.](common/select-azuread.png)

1. Válassza ki **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Egy alkalmazás hozzáadásához válassza **új alkalmazás**.

    ![Az új alkalmazás-beállítás](common/add-new-app.png)

1. A Keresés mezőbe írja be a **monday.com**. A keresési eredmények között, válassza ki a **monday.com**, majd válassza ki **Hozzáadás**.

    ![az eredmények listájában Monday.com](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az nevű tesztfelhasználó alapján monday.com **Britta Simon**. Az egyszeri bejelentkezés működéséhez ki kell alakítani a társított kapcsolatot egy Azure AD-felhasználót és a kapcsolódó felhasználó között monday.com.

Az Azure AD egyszeri bejelentkezés az monday.com tesztelése és konfigurálása, hogy a következő építőelemeit kell elvégeznie:

| Feladat | Leírás |
| --- | --- |
| **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)** | Lehetővé teszi a felhasználók a funkció használatához. |
| **[Monday.com egyszeri bejelentkezés konfigurálása](#configure-mondaycom-single-sign-on)** | Az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazásban. |
| **[Hozzon létre egy Azure ad-ben tesztfelhasználó számára](#create-an-azure-ad-test-user)** | Tesztek az Azure AD egyszeri bejelentkezés egy felhasználó nevű Britta Simon. |
| **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assign-the-azure-ad-test-user)** | Britta Simon az Azure AD egyszeri bejelentkezés használata lehetővé teszi. |
| **[Monday.com tesztfelhasználó létrehozása](#create-a-mondaycom-test-user)** | Létrehoz egy megfelelője a Britta Simon monday.com, amely a felhasználó Azure ad-ben reprezentációja van csatolva. |
| **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** | Ellenőrzi, hogy működik-e a konfiguráció. |

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban konfigurálnia az Azure AD egyszeri bejelentkezés az Azure Portalon monday.com.

1. Az a [az Azure portal](https://portal.azure.com/), a a **monday.com** application integration ablaktáblában válassza **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az a **egyszeri bejelentkezési módszer** ablaktáblán válassza ki az **SAML** vagy **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

1. Az a **állítsa be egyszeri bejelentkezést az SAML** ablaktáblán válassza **szerkesztése** (a ceruza ikon) nyissa meg a **alapszintű SAML-konfigurációja** ablaktáblán.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** ablaktáblán, ha rendelkezik a service provider metaadatait tartalmazó fájl, és a konfigurálni kívánt *Identitásszolgáltató által kezdeményezett mód*, kövesse az alábbi lépéseket:

    1. Válassza ki **metaadatfájl feltöltése**.

       ![Metaadatok feltöltés beállítást](common/upload-metadata.png)

    1. Válassza ki a metaadatait tartalmazó fájl, a mappa ikonra, és válassza **feltöltése**.

       ![Válassza ki a metaadat-fájlt, majd a feltöltés gombbal](common/browse-upload-metadata.png)

    1. A metaadatait tartalmazó fájl sikeresen feltöltve, miután a **azonosító** és **válasz URL-cím** értékeket automatikusan fel van töltve a a **alapszintű SAML-konfigurációja** panelen:

       ![Az IDP értékeket az alapszintű SAML-konfigurációja panelen](common/idp-intiated.png)

       > [!Note]
       > Ha a **azonosító** és **válasz URL-cím** értékek nincsenek automatikusan kitöltve, adja meg az értékeket manuálisan.

1. Az alkalmazás konfigurálása *SP által kezdeményezett mód*:

    1. Válassza ki **további URL-címet beállítani**.
    
    1. Az a **bejelentkezési URL-cím** mezőbe írjon be egy URL-címet, amely rendelkezik a következő mintának: https:\//\<tartomanynev >. monday.com. Forduljon a [monday.com ügyfél-támogatási csapatának](mailto:support@monday.com) a bejelentkezési URL-Címének lekéréséhez.

        ![További URL-címek Set kapcsoló](common/metadata-upload-additional-signon.png)

1. Monday.com alkalmazása a SAML helyességi feltételek egy meghatározott formátumban kell megadni. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezeket az attribútumértékeket, kezelhet a **állítsa be egyszeri bejelentkezést az SAML** panelen válassza **szerkesztése** megnyitásához a **felhasználói attribútumok** ablaktáblán.

    ![A felhasználói attribútumok ablaktáblából.](common/edit-attribute.png)

1. A **felhasználói jogcímek**válassza **szerkesztése** a jogcímek szerkesztése. Jogcím hozzáadásához válassza **hozzáadása új jogcímet**, majd adja meg az SAML-jogkivonat attribútum az előző képen látható módon. Ezután kövesse az alábbi lépéseket: 

    1. Válassza ki **hozzáadása új jogcímet**.

        ![A Hozzáadás, új jogcím-beállítás a felhasználói jogcímek panelen](common/new-save-attribute.png)

    1. Az a **kezelheti a felhasználói jogcímek** panelen adja meg a következő értékeket:
        
       1. Az a **neve** mezőben megadhatja azon attribútum nevét, a felhasználói jogcím sor is látható.

       1. Hagyja **Namespace** üres.

       1. A **forrás**válassza **attribútum**.

       1. Az a **forrásattribútum** listájához, válassza ki a felhasználói jogcím sorhoz feltüntetett attribútumértéket.

       1. Válassza ki **OK**, majd válassza ki **mentése**.

       ![A kezelés felhasználói jogcímek](common/new-attribute-details.png)

1. Az a **állítsa be egyszeri bejelentkezést az SAML** panel alatt **SAML-aláíró tanúsítvány**, jelölje be **letöltése** melletti **tanúsítvány (Base64)**. Válassza ki a letöltési lehetőséget igényei alapján. Mentse a tanúsítványt a számítógépen.

    ![A tanúsítvány (Base64) a letöltési lehetőséget](common/certificatebase64.png)

1. Az a **monday.com beállítása** területén másolja a következő URL-címek igényei alapján:

    * Bejelentkezési URL
    * Azure AD-azonosító
    * Kijelentkezési URL

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-mondaycom-single-sign-on"></a>Monday.com egyszeri bejelentkezés konfigurálása

Egyszeri bejelentkezés konfigurálásához a monday.com oldalon, a letöltött tanúsítvány (Base64) fájlt, és a megfelelő URL-címeket, az Azure Portalról másolt küldése a [monday.com támogatási csapatának](mailto:support@monday.com). A monday.com csapatával küldheti el nekik, győződjön meg arról, hogy a SAML egyszeri bejelentkezési kapcsolatot helyesen van beállítva mindkét oldalon az információkat használja.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban hozzon létre egy tesztfelhasználót Britta Simon nevű az Azure Portalon.

1. Az Azure Portalon válassza ki a **Azure Active Directory** > **felhasználók** > **minden felhasználó**.

    ![A felhasználók és az összes felhasználói beállítások](common/users.png)

1. Válassza ki **új felhasználó**.

    ![Az új felhasználói beállítás](common/new-user.png)

1. Az a **felhasználói** panelen a következő lépéseket:

    1. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    1. Az a **felhasználónév** mezőbe írja be **brittasimon\@\<a vállalati tartomány >.\< bővítmény >**. Ha például **brittasimon\@contoso.com**.

    1. Válassza ki a **Show jelszó** jelölőnégyzetet. Írja le az értéket, a megjelenő a **jelszó** mezőbe.

    1. Kattintson a **Létrehozás** gombra.

    ![A felhasználói panelen](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Ön hozzáférést Britta Simon monday.com így ő Azure egyszeri bejelentkezéshez használható.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások** > **minden alkalmazás** > **monday.com**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

1. Az alkalmazások listájában jelölje ki a **monday.com**.

    ![az alkalmazások listáját a Monday.com](common/all-applications.png)

1. Válassza a menüben **felhasználók és csoportok**.

    ![A felhasználók és csoportok lehetőség](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**. Ezt követően a a **-hozzárendelés hozzáadása** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A Hozzáadás hozzárendelési ablaktáblán](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** ablaktáblán válassza **Britta Simon** felhasználók listájában. Válassza a **Kiválasztás** lehetőséget

1. Ha az a SAML-előfeltétel szerepkör értéket várt a **szerepkör kiválasztása** ablaktáblán válassza ki a megfelelő szerepkört a felhasználóhoz a listából. Válassza a **Kiválasztás** lehetőséget

1. Az a **hozzárendelés hozzáadása** ablaktáblán válassza előbb **hozzárendelése**.

### <a name="create-a-mondaycom-test-user"></a>Monday.com tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót a monday.com alkalmazásban jön létre. Monday.com támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó már nem létezik az monday.com, egy új jön létre a hitelesítés után.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban a az Azure AD egyszeri bejelentkezés beállításai a saját alkalmazások portál segítségével tesztelnie.

Miután beállította egyszeri bejelentkezést, amikor kiválaszt **monday.com** a saját alkalmazások portál automatikusan bejelentkezett a monday.com. A saját alkalmazások portál kapcsolatos további információkért lásd: [használatának és elérésének alkalmazásokat a saját alkalmazások portál](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket:

- [Az Azure Active Directory SaaS-alkalmazások integrálását ismertető oktatóanyagok listáját](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Egyszeri bejelentkezés az Azure Active Directory-alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
