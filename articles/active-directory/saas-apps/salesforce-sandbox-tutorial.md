---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, a Salesforce-tesztkörnyezet |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Salesforce-tesztkörnyezet között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: 33d39e617bd55b88d8f04785d937faf176463ee5
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808816"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Oktatóanyag: Az Azure Active Directory-integráció, a Salesforce-tesztkörnyezet

Ebben az oktatóanyagban elsajátíthatja, hogyan Salesforce védőfal integrálása az Azure Active Directory (Azure AD).

Próbakörnyezetbe lefordítja lehetővé teszik, a szervezet több példányának létrehozása a külön környezetek esetében különböző célokra, például a fejlesztés, tesztelés és képzésekről, az adatok és alkalmazások, a Salesforce éles környezetben veszélyeztetése nélkül szervezet.
További részletekért lásd: [tesztkörnyezet áttekintése](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

Az Azure AD integrálása a Salesforce védőfal nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Salesforce-tesztkörnyezet Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Salesforce védőfal (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Salesforce-tesztkörnyezet, a következőkre van szükség:

- Azure AD-előfizetés
- A Salesforce-tesztkörnyezet egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Salesforce-tesztkörnyezet hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Salesforce-tesztkörnyezet hozzáadása a katalógusból

Az Azure AD integrálása a Salesforce-tesztkörnyezet konfigurálása, hozzá kell Salesforce védőfal a galériából a felügyelt SaaS-alkalmazások listájára.

**Salesforce-tesztkörnyezet hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Salesforce védőfal**, jelölje be **Salesforce védőfal** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában a Salesforce-tesztkörnyezet](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és tesztelés az Azure AD egyszeri bejelentkezés Salesforce védőfal "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a Salesforce tesztkörnyezetben tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval hivatkozás kapcsolatot egy Azure AD-felhasználót és a kapcsolódó felhasználó között a Salesforce-tesztkörnyezet kell létrehozni.

Salesforce-tesztkörnyezet, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a Salesforce-tesztkörnyezet tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Salesforce védőfal tesztfelhasználót](#create-a-salesforce-sandbox-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása Salesforce tesztkörnyezetben.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon és a Salesforce védőfal alkalmazását az egyszeri bejelentkezés konfigurálása.

**A Salesforce-tesztkörnyezet konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Salesforce védőfal** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Kattintson a **módosítása egyszeri bejelentkezési mód** felett válassza ki a képernyő a **SAML** mód.

      ![Egyszeri bejelentkezési hivatkozás konfigurálása](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.
   
    ![Egyszeri bejelentkezési hivatkozás konfigurálása](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. Az a **alapszintű SAML-konfigurációja** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    a. Kattintson a **metaadatfájl feltöltése**.

    ![Metaadatfájl feltöltése](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![metaadatfájl kiválasztása](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > A Salesforce-tesztkörnyezet felügyeleti portálon, az oktatóanyag későbbi részében ismertetett a szolgáltató metaadat-fájlt fog kapni.

    c. A metaadatfájl sikeres feltöltése után a **válasz URL-cím** érték jelenik meg automatikusan a **válasz URL-cím** szövegmezőbe.

    ![A Salesforce védőfal tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. Az a **SAML-aláíró tanúsítvány** szakaszt, kattintson a **letöltése** letöltéséhez **összevonási metaadatainak XML** , és mentse az xml-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a Salesforce-tesztkörnyezet rendszergazdai fiókjával.

8. Kattintson a a **telepítő** alatt **beállítások ikon** az oldal jobb felső sarkában található.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure1.png)

9. Görgessen le a **beállítások** a bal oldali navigációs ablaktáblán kattintson a **identitás** a kapcsolódó szakasz kibontásához. Kattintson a **egyszeri bejelentkezési beállításainak**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Az a **egyszeri bejelentkezési beállításainak** lap, kattintson a **szerkesztése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure3.png)

11. Válassza ki **SAML engedélyezett**, és kattintson a **mentése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Az SAML egyszeri bejelentkezési beállításainak konfigurálásához kattintson **új metaadatait tartalmazó fájl a**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. Kattintson a **fájl kiválasztása** az metaadatainak XML-fájl, amelyre letöltötte az Azure Portalon, majd kattintson a feltöltendő **létrehozás**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. Az a **SAML egyszeri bejelentkezési beállításainak** lapon mezők automatikusan feltölti és kattintson a Mentés gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/salesforcexml.png)

15. Az a **egyszeri bejelentkezési beállításainak** lap, kattintson a **metaadatok letöltése** gombra kattintva töltse le a service provider metaadatait tartalmazó fájl. A fájlt a **alapszintű SAML-konfigurációja** szakasz a szükséges URL-címek konfigurálásához, amint azt fent kifejtettük, az Azure Portalon.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure4.png)

16. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód, a következő előfeltételekkel, amely rendelkezik:

    a. Rendelkeznie kell egy ellenőrzött tartomány.

    b. Ennek lépéseit magyarázatát konfigurálásához és a tartományt, a Salesforce-tesztkörnyezet engedélyezéséhez van szüksége, az oktatóanyag későbbi részében.

    c. Az Azure Portalon az a **alapszintű SAML-konfigurációja** területén kattintson **további URL-címet beállítani** , és hajtsa végre a következő lépést:
  
    ![A Salesforce védőfal tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az értéket a következő minta használatával: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Ez az érték a Salesforce-tesztkörnyezet portálról kell másolni, miután engedélyezte a tartományhoz.

17. Az a **SAML-aláíró tanúsítvány** területén kattintson **összevonási metaadatainak XML** , és mentse az xml-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a Salesforce-tesztkörnyezet rendszergazdai fiókjával.

19. Kattintson a a **telepítő** alatt **beállítások ikon** az oldal jobb felső sarkában található.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure1.png)

20. Görgessen le a **beállítások** a bal oldali navigációs ablaktáblán kattintson a **identitás** a kapcsolódó szakasz kibontásához. Kattintson a **egyszeri bejelentkezési beállításainak**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. Az a **egyszeri bejelentkezési beállításainak** lap, kattintson a **szerkesztése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure3.png)

22. Válassza ki **SAML engedélyezett**, és kattintson a **mentése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. Az SAML egyszeri bejelentkezési beállításainak konfigurálásához kattintson **új metaadatait tartalmazó fájl a**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. Kattintson a **fájl kiválasztása** az XML-metaadatfájl feltöltésére, és kattintson a **létrehozás**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. Az a **SAML egyszeri bejelentkezési beállításainak** lapon mezők automatikusan feltölti, írja be a konfiguráció nevét (például: *SPSSOWAAD_Test*) az a **neve** szövegmezőbe, majd kattintson a mentés.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

26. Ahhoz, hogy a tartományt, a Salesforce-tesztkörnyezet, hajtsa végre az alábbi lépéseket:

    > [!NOTE]
    > A tartomány engedélyezése előtt hozzon létre ugyanaz a Salesforce-tesztkörnyezet kell. További információkért lásd: [meghatározása a tartománynév](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). A tartomány létrehozása után győződjön meg arról, hogy megfelelően van konfigurálva.

    * A Salesforce-tesztkörnyezetben a bal oldali navigációs panelén kattintson **vállalati beállítások** bontsa ki a kapcsolódó csomópontot, majd **saját tartomány**.

         ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * Az a **hitelesítési konfiguráció** területén kattintson **szerkesztése**.

        ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * Az a **hitelesítési konfiguráció** szakaszban, **hitelesítési szolgáltatás**, válassza ki a nevét, a SAML egyszeri bejelentkezés beállítást, amely a Salesforce-tesztkörnyezet egyszeri bejelentkezés konfigurálása során meg van, és Kattintson a **mentése**.

        ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="create-a-salesforce-sandbox-test-user"></a>Salesforce-tesztkörnyezet tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó Salesforce tesztkörnyezetben jön létre. Salesforce-tesztkörnyezet támogatja a just-in-time-kiépítés, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik, a Salesforce-tesztkörnyezet, egy új jön létre, a Salesforce-tesztkörnyezet elérése megkísérlésekor. Salesforce-tesztkörnyezet is támogatja a felhasználók automatikus átadása, további részleteket talál [Itt](salesforce-sandbox-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon ad hozzáférést, a Salesforce védőfal által használandó Azure egyszeri bejelentkezést.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel a Salesforce-tesztkörnyezet, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Salesforce védőfal**.

    ![A Salesforce-tesztkörnyezet hivatkozásra az alkalmazások listáját](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **felhasználó hozzáadása** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panel a Salesforce védőfal csempére kattint, meg kell lekérése automatikusan bejelentkezett a védőfal Salesforce alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](salesforce-sandbox-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png