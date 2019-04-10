---
title: 'Oktatóanyag: A Citrix Netscaler integrálása az Azure Active Directory |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Citrix Netscaler között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 6d434295a6a46ee5b7089608cbf788ff91589fb7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281675"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>Oktatóanyag: A Citrix Netscaler integrálása az Azure Active Directory

Ebben az oktatóanyagban elsajátíthatja, hogyan Citrix Netscaler integrálása az Azure Active Directory (Azure AD).
A Citrix Netscaler integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Citrix Netscaler Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett a Citrix Netscaler (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Citrix Netscaler, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* A Citrix Netscaler egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Citrix Netscaler **SP** által kezdeményezett egyszeri bejelentkezés

* Támogatja a Citrix Netscaler **igény szerinti** felhasználók átadása

## <a name="adding-citrix-netscaler-from-the-gallery"></a>A Citrix Netscaler hozzáadása a katalógusból

A Citrix Netscaler integrálása az Azure AD beállítása, hozzá kell a Citrix Netscaler a galériából a felügyelt SaaS-alkalmazások listájára.

**A Citrix Netscaler hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Citrix Netscaler**, jelölje be **Citrix Netscaler** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![A Citrix Netscaler a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és az Azure AD egyszeri bejelentkezés tesztelése a Citrix Netscaler alapján nevű tesztfelhasználó **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó a Citrix Netscaler hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés a Citrix Netscaler tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[A Citrix Netscaler egyszeri bejelentkezés konfigurálása](#configure-citrix-netscaler-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[A Citrix Netscaler tesztfelhasználó létrehozása](#create-citrix-netscaler-test-user)**  – van egy Britta Simon megfelelője a Citrix Netscaler, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása a Citrix Netscaler, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Citrix Netscaler** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![A Citrix Netscaler tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<<Your FQDN>>`

    c. Az a **válasz URL-cím (helyességi feltétel fogyasztói szolgáltatás URL-címe)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [Citrix Netscaler ügyfél-támogatási csapatának](https://www.citrix.com/contact/technical-support.html) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    > [!NOTE]
    > Egyszeri bejelentkezés használata, hogy az URL-címek nyilvános helyek elérhetőnek kell lenniük. A tűzfal vagy más biztonsági beállítások az Azure ad-ben a jogkivonat közzététele a konfigurált ACS URL-címen enble Netscaler oldalán engedélyeznie kell.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **állítsa be a Citrix Netscaler** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-citrix-netscaler-single-sign-on"></a>A Citrix Netscaler egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, a bejelentkezés a Citrix Netscaler bérlői rendszergazdaként.

2. Győződjön meg arról, hogy a **NetScaler belső vezérlőprogram verziója = NS12.1: Build 48.13.nc**.

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure01.png)

3. Az a **virtuális VPN-kiszolgáló** lapon, a következő lépésekkel:

     ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure02.png)

    a. Állítsa be az átjáró beállításainak **ICA csak** , **igaz**.
    
    b. Állítsa be **hitelesítés engedélyezése** , **igaz**.
    
    c. **DTLS** nem kötelező.
    
    d. Győződjön meg arról, hogy **SSLv3** , **letiltott**.

4. A testre szabott **SSL-Rejtjelek** csoport jön létre A + az áramfelhasználásra https://www.ssllabs.com alább látható módon:

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure03.png)

5. Az a **hitelesítési SAML kiszolgáló konfigurálása** lapon, a következő lépésekkel:

      ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure04.png)

    a. Az a **neve** szövegmezőbe írja be a kiszolgáló nevét.

    b. Az a **átirányítási URL-cím** szövegmezőjébe illessze be az értéket, **bejelentkezési URL-cím** az Azure Portalról másolt.

    c. Az a **egyszeri kijelentkezési URL-cím** szövegmezőjébe illessze be az értéket a **kijelentkezési URL-címe** az Azure Portalról másolt.

    d. A **Identitásszolgáltató tanúsítvány neve**, kattintson a **"+"** vegye fel a tanúsítványt, amely az Azure Portalról letöltött jelentkezik. A feltöltés után válasszon ki a tanúsítványt a legördülő listából.

    e. A következő további mezőket kell állítani ezen az oldalon

      ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure24.png)

    f. Válassza ki **kért hitelesítési környezetet** , **pontos**.

    g. Válassza ki **aláírási algoritmus** , **RSA-SHA256 algoritmust**.

    h. Válassza ki **kivonatoló módszerrel** , **SHA256**.

    i. Ellenőrizze **felhasználónév kényszerítése**.

    j. Kattintson az **OK** gombra

6. Konfigurálhatja a **munkamenet profil**, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure06.png)

    a. Az a **neve** szövegmezőbe írja be a munkamenet-profil nevét.

    b. Az a **Ügyfélélmény** lapra, végezze el a módosításokat, az alábbi képernyőképen látható módon.

    c. Továbbra is a módosításokat a **az Általános lapon** alább látható módon kattintson **OK**

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure07.png)

    d. Az a **közzétett alkalmazások** lapon hajtsa végre a módosításokat, az alábbi képernyőképen látható módon, és kattintson a **OK**.

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure08.png)

    e. Az a **biztonsági** lapon hajtsa végre a módosításokat, az alábbi képernyőképen látható módon, és kattintson a **OK**.

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure09.png)

7. A csatlakozás a munkamenet megbízhatóság porton ICA kapcsolatok **2598** , ahogyan az alábbi képernyőképet.

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure10.png)

8. Az a **SAML** területen adja hozzá a **kiszolgálók** az alábbi képernyőképen látható módon.

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure11.png)

9. Az a **SAML** területen adja hozzá a **házirendek** az alábbi képernyőképen látható módon.

     ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure12.png)

10. Az a **globális beállítások** lapon kattintson a **objektumkiszolgálón hozzáférés** szakaszban.

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure13.png)

11. Az a **konfigurációs** fülre, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure14.png)

    a. Válassza ki **lehetővé teszi a tartományok**.

    b. Az a **tartománynév** szövegmezőben válassza ki a tartományt.

    c. Kattintson az **OK** gombra.

12. Győződjön meg arról, a **kirakat** beállításait a **webhelyek fogadó** az alábbi képernyőképen látható módon:

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure15.png)

13. Az a **kezelése hitelesítési módszerek – Corp** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure16.png)

    a. Válassza ki **felhasználónevet és jelszót**.

    b. Válassza ki **NetScaler-átjárón átmenő**.

    c. Kattintson az **OK** gombra.

14. Az a **megbízható tartományok konfigurálása** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure17.png)

    a. Válassza ki **megbízható tartományok csak**.

    b. Kattintson a **Hozzáadás** hozzáadhatja a tartományt a **megbízható tartományok** szövegmezőbe.

    c. Válassza ki az alapértelmezett tartomány a **alapértelmezett tartomány** listája.

    d. Válassza ki **Show tartományok listája a bejelentkezési oldal**.

    e. Kattintson az **OK** gombra.

15. Az a **NetScaler átjárók kezelése** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure18.png)

    a. Kattintson a **Hozzáadás** hozzáadása a NetScaler Gateways in **NetScaler átjárók** szövegmezőbe.

    b. Kattintson a **Bezárás** gombra.

16. Az a **kirakat az általános beállítások** fülre, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure19.png)

    a. Az a **megjelenítendő név** szövegmezőbe írja be a NetScaler átjárókiszolgáló nevét.

    b. Az a **NetScaler átjáró URL-címe** szövegmezőbe írja be a NetScaler átjáró URL-CÍMÉT.

    c. Válassza ki **használati vagy szerepkör** , **hitelesítési és HDX útválasztási**.

    d. Kattintson az **OK** gombra.

17. Az a **kirakat biztonságos jegy hatóság** fülre, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure20.png)

    a. Kattintson a **Hozzáadás** gombra kattintva adhat hozzá a **biztonságos jegy szolgáltató URL-CÍMEK** be a szövegmezőbe.

    b. Válassza ki **munkamenet megbízhatóság engedélyezéséhez**.

    c. Kattintson az **OK** gombra.

18. Az a **kirakat hitelesítési beállítások** fülre, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure21.png)

    a. Válassza ki a **verzió**.

    b. Válassza ki **bejelentkezés típusa** , **tartomány**.

    c. Adja meg a **visszahívási URL-Címének**.

    d. Kattintson az **OK** gombra.

19. Az a **kirakat üzembe Citrix fogadó** fülre, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure22.png)

    a. Válassza ki **üzembe helyezési lehetőség** , **HTML5, ha nem érhető el helyi fogadó használja fogadót**.

    b. Kattintson az **OK** gombra.

20. Az a **kezelése jeleket** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/citrix-netscaler-tutorial/configure23.png)

    a. Válassza ki a **belső szóró** , **a szolgáltatás URL-cím használata**.

    b. Kattintson a **hozzáadása** hozzáadása az URL az a **külső jeleket** szövegmezőbe.

    c. Kattintson az **OK** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés a Citrix Netscaler Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Citrix Netscaler**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Citrix Netscaler**.

    ![A Citrix Netscaler hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-citrix-netscaler-test-user"></a>A Citrix Netscaler tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó a Citrix Netscaler jön létre. A Citrix Netscaler támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a Citrix Netscaler, egy új jön létre a hitelesítés után.

>[!NOTE]
>Hozzon létre egy felhasználót manuálisan kell, ha kapcsolódni kell a [Citrix Netscaler ügyfél-támogatási csapatának](https://www.citrix.com/contact/technical-support.html).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Citrix Netscaler csempére kattint, meg kell lehet automatikusan bejelentkezett a Citrix Netscaler, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

