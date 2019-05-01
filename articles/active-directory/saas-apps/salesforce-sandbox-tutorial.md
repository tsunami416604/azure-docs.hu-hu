---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, a Salesforce-tesztkörnyezet |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Salesforce-tesztkörnyezet között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: b2940f3eee3112fe1c6d57cc92157c573ecad109
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64722568"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Oktatóanyag: Az Azure Active Directory-integráció, a Salesforce-tesztkörnyezet

Ebben az oktatóanyagban elsajátíthatja, hogyan Salesforce védőfal integrálása az Azure Active Directory (Azure AD).

Próbakörnyezetbe lefordítja lehetővé teszik, a szervezet több példányának létrehozása a külön környezetek esetében különböző célokra, például a fejlesztés, tesztelés és képzésekről, az adatok és alkalmazások, a Salesforce éles környezetben veszélyeztetése nélkül szervezet.
További részletekért lásd: [tesztkörnyezet áttekintése](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

Az Azure AD integrálása a Salesforce védőfal nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Salesforce-tesztkörnyezet Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett a Salesforce védőfal (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Salesforce-tesztkörnyezet, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Salesforce-tesztkörnyezet egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Salesforce-tesztkörnyezet **SP és IDP** által kezdeményezett egyszeri bejelentkezés
* Támogatja a Salesforce-tesztkörnyezet **igény szerinti** felhasználók átadása
* Támogatja a Salesforce-tesztkörnyezet [ **automatikus** felhasználók átadása](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Salesforce-tesztkörnyezet hozzáadása a katalógusból

Az Azure AD integrálása a Salesforce-tesztkörnyezet konfigurálása, hozzá kell Salesforce védőfal a galériából a felügyelt SaaS-alkalmazások listájára.

**Salesforce-tesztkörnyezet hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Salesforce védőfal**, jelölje be **Salesforce védőfal** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában a Salesforce-tesztkörnyezet](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálni és tesztelés az Azure AD egyszeri bejelentkezés Salesforce védőfal alapján nevű tesztfelhasználó **Britta Simon**.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a Salesforce tesztkörnyezetben tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval hivatkozás kapcsolatot egy Azure AD-felhasználót és a kapcsolódó felhasználó között a Salesforce-tesztkörnyezet kell létrehozni.

Az Azure AD egyszeri bejelentkezés a Salesforce-tesztkörnyezet tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[A Salesforce védőfal egyszeri bejelentkezés konfigurálása](#configure-salesforce-sandbox-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre a Salesforce-tesztkörnyezet tesztfelhasználót](#create-salesforce-sandbox-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása Salesforce tesztkörnyezetben.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

A Salesforce-tesztkörnyezet konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Salesforce védőfal** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszt, ha rendelkezik **szolgáltató metaadatait tartalmazó fájl** és való konfigurálásához a **Identitásszolgáltató** kezdeményezett módban a következő lépésekkel:

    a. Kattintson a **metaadatfájl feltöltése**.

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    > [!NOTE]
    > A Salesforce-tesztkörnyezet felügyeleti portálon, az oktatóanyag későbbi részében ismertetett a szolgáltató metaadat-fájlt fog kapni.

    c. A metaadatfájl sikeres feltöltése után a **válasz URL-cím** érték jelenik meg automatikusan a **válasz URL-cím** szövegmezőbe.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Ha a **válasz URL-cím** értéke nem kérdezhető le az automatikus polulated, majd töltse ki a követelmény alapján manuálisan az értéket.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **metaadatainak XML**a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **Salesforce-tesztkörnyezet beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-salesforce-sandbox-single-sign-on"></a>Salesforce-tesztkörnyezet egyszeri bejelentkezés konfigurálása

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a Salesforce-tesztkörnyezet rendszergazdai fiókjával.

2. Kattintson a a **telepítő** alatt **beállítások ikon** az oldal jobb felső sarkában található.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure1.png)

3. Görgessen le a **beállítások** a bal oldali navigációs ablaktáblán kattintson a **identitás** a kapcsolódó szakasz kibontásához. Kattintson a **egyszeri bejelentkezési beállításainak**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Az a **egyszeri bejelentkezési beállításainak** lap, kattintson a **szerkesztése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure3.png)

5. Válassza ki **SAML engedélyezett**, és kattintson a **mentése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Az SAML egyszeri bejelentkezési beállításainak konfigurálásához kattintson **új metaadatait tartalmazó fájl a**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Kattintson a **fájl kiválasztása** az metaadatainak XML-fájl, amelyre letöltötte az Azure Portalon, majd kattintson a feltöltendő **létrehozás**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Az a **SAML egyszeri bejelentkezési beállításainak** lapon mezők automatikusan feltölti és kattintson a Mentés gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Az a **egyszeri bejelentkezési beállításainak** lap, kattintson a **metaadatok letöltése** gombra kattintva töltse le a service provider metaadatait tartalmazó fájl. A fájlt a **alapszintű SAML-konfigurációja** szakasz a szükséges URL-címek konfigurálásához, amint azt fent kifejtettük, az Azure Portalon.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure4.png)

10. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód, a következő előfeltételekkel, amely rendelkezik:

    a. Rendelkeznie kell egy ellenőrzött tartomány.

    b. Ennek lépéseit magyarázatát konfigurálásához és a tartományt, a Salesforce-tesztkörnyezet engedélyezéséhez van szüksége, az oktatóanyag későbbi részében.

    c. Az Azure Portalon az a **alapszintű SAML-konfigurációja** területén kattintson **további URL-címet beállítani** , és hajtsa végre a következő lépést:
  
    ![A Salesforce védőfal tartomány és URL-címeket egyetlen bejelentkezési adatait](common/both-signonurl.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az értéket a következő minta használatával: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Ez az érték a Salesforce-tesztkörnyezet portálról kell másolni, miután engedélyezte a tartományhoz.

11. Az a **SAML-aláíró tanúsítvány** területén kattintson **összevonási metaadatainak XML** , és mentse az xml-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

12. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a Salesforce-tesztkörnyezet rendszergazdai fiókjával.

13. Kattintson a a **telepítő** alatt **beállítások ikon** az oldal jobb felső sarkában található.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure1.png)

14. Görgessen le a **beállítások** a bal oldali navigációs ablaktáblán kattintson a **identitás** a kapcsolódó szakasz kibontásához. Kattintson a **egyszeri bejelentkezési beállításainak**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Az a **egyszeri bejelentkezési beállításainak** lap, kattintson a **szerkesztése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure3.png)

16. Válassza ki **SAML engedélyezett**, és kattintson a **mentése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Az SAML egyszeri bejelentkezési beállításainak konfigurálásához kattintson **új metaadatait tartalmazó fájl a**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Kattintson a **fájl kiválasztása** az XML-metaadatfájl feltöltésére, és kattintson a **létrehozás**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Az a **SAML egyszeri bejelentkezési beállításainak** lapon mezők automatikusan feltölti, írja be a konfiguráció nevét (például: *SPSSOWAAD_Test*), a a **neve** szövegmezőbe, majd kattintson a mentés.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Ahhoz, hogy a tartományt, a Salesforce-tesztkörnyezet, hajtsa végre az alábbi lépéseket:

    > [!NOTE]
    > A tartomány engedélyezése előtt hozzon létre ugyanaz a Salesforce-tesztkörnyezet kell. További információkért lásd: [meghatározása a tartománynév](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). A tartomány létrehozása után győződjön meg arról, hogy megfelelően van konfigurálva.

21. A Salesforce-tesztkörnyezetben a bal oldali navigációs panelén kattintson **vállalati beállítások** bontsa ki a kapcsolódó csomópontot, majd **saját tartomány**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Az a **hitelesítési konfiguráció** területén kattintson **szerkesztése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Az a **hitelesítési konfiguráció** szakaszban, **hitelesítési szolgáltatás**, válassza ki a nevét, a SAML egyszeri bejelentkezés beállítást, amely a Salesforce-tesztkörnyezet egyszeri bejelentkezés konfigurálása során meg van, és Kattintson a **mentése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com.

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon ad hozzáférést, a Salesforce védőfal által használandó Azure egyszeri bejelentkezést.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Salesforce védőfal**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Salesforce védőfal**.

    ![A Salesforce-tesztkörnyezet hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-salesforce-sandbox-test-user"></a>Salesforce-tesztkörnyezet tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó Salesforce tesztkörnyezetben jön létre. Salesforce-tesztkörnyezet támogatja a just-in-time-kiépítés, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik, a Salesforce-tesztkörnyezet, egy új jön létre, a Salesforce-tesztkörnyezet elérése megkísérlésekor. Salesforce-tesztkörnyezet is támogatja a felhasználók automatikus átadása, további részleteket talál [Itt](salesforce-sandbox-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panel a Salesforce védőfal csempére kattint, akkor kell automatikusan megtörténik a a Salesforce próbakörnyezetben, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Felhasználók átadásának konfigurálása](salesforce-sandbox-provisioning-tutorial.md)
