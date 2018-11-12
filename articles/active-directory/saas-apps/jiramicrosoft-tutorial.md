---
title: 'Oktatóanyag: Azure Active Directory-integráció a Microsoft által a JIRA SAML SSO-val |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a JIRA SAML egyszeri bejelentkezés a Microsoft között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: jeedes
ms.openlocfilehash: f3f7fc3b837dd4eef9bab8ff34a36329436bad9a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010728"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft"></a>Oktatóanyag: Azure Active Directory-integráció a Microsoft által a JIRA SAML SSO-val

Ebben az oktatóanyagban elsajátíthatja a Microsoft által a JIRA SAML SSO integrálása az Azure Active Directory (Azure AD).

Microsoft JIRA SAML SSO integrálása az Azure AD nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a JIRA SAML egyszeri bejelentkezés a Microsoft Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a JIRA SAML SSO (egyszeri bejelentkezés) a Microsoft által az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="description"></a>Leírás

Egyszeri bejelentkezés engedélyezéséhez használja a Microsoft Azure Active Directory-fiók Atlassian JIRA-kiszolgálóval. Ezzel a módszerrel a munkahely összes felhasználója számára a bejelentkezés a JIRA alkalmazásba használhatja az Azure AD hitelesítő adatait. Ez a beépülő modul SAML 2.0 összevonási használ.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Microsoft által a JIRA SAML SSO-val, a következőkre van szükség:

- Azure AD-előfizetés
- JIRA Core és a szoftver 6.0 7.12, vagy a JIRA szolgáltatás ügyfélszolgálati 3.0, 3.5-ös telepítenie kell, és a Windows 64 bites verzióját a következőn:
- JIRA-kiszolgáló a HTTPS-kompatibilis
- Vegye figyelembe, hogy a támogatott verziók JIRA beépülő modul szakasz alatt található szerepelnek.
- JIRA-kiszolgáló elérhető-e internetes különösen az Azure AD bejelentkezési oldal a hitelesítéshez, és tudja fogadni a jogkivonatot az Azure ad-ből kell
- JIRA-hibajegy rendszergazdai hitelesítő adatok beállítása
- WebSudo JIRA-hibajegy le van tiltva
- A JIRA kiszolgálói alkalmazásban létrehozott tesztfelhasználó számára

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem javasoljuk a JIRA éles környezetben. Az integráció először tesztelje fejlesztési vagy az alkalmazás átmeneti környezetben, majd az éles környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [próba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>JIRA támogatott verziói

* JIRA-Core és a szoftver: 6.0 való 7.12
* JIRA-ügyfélszolgálat 3.0.0-s való 3.5.0
* JIRA 5.2 is támogatja. További részletekért kattintson [Microsoft Azure Active Directory egyszeri bejelentkezés a JIRA 5.2.](jira52microsoft-tutorial.md)

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A Microsoft által a JIRA SAML SSO hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>A Microsoft által a JIRA SAML SSO hozzáadása a katalógusból

Az Azure AD-be a Microsoft által a JIRA SAML SSO-integráció konfigurálásához, hozzá kell JIRA SAML egyszeri bejelentkezés a Microsoft által a galériából a felügyelt SaaS-alkalmazások listájára.

**A Microsoft által a JIRA SAML SSO hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **JIRA SAML egyszeri bejelentkezés a Microsoft**, jelölje be **JIRA SAML egyszeri bejelentkezés a Microsoft** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![JIRA SAML egyszeri bejelentkezés a Microsoft a találatok listájában](./media/jiramicrosoft-tutorial/tutorial_singlesign-onforjira_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Microsoft a teszt "Britta Simon" nevű felhasználó JIRA SAML SSO-val.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a JIRA SAML egyszeri bejelentkezés a Microsoft partner felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó JIRA SAML egyszeri bejelentkezés a Microsoft által a hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a Microsoft által a JIRA SAML SSO-val tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[JIRA SAML egyszeri bejelentkezés a Microsoft a teszt felhasználó létrehozása](#creating-jira-saml-sso-by-microsoft-test-user)**  – egy megfelelője a Britta Simon JIRA SAML SSO, amely kapcsolódik az Azure AD felhasználói ábrázolása a Microsoft által van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és egyszeri bejelentkezés Microsoft-alkalmazáshoz a JIRA SAML SSO beállításához.

**Az Azure AD egyszeri bejelentkezés konfigurálása a Microsoft által a JIRA SAML SSO-val, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **JIRA SAML egyszeri bejelentkezés a Microsoft** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial_general_301.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![JIRA SAML SSO által a Microsoft-Domain és URL-címek egyetlen bejelentkezési adatait](./media/jiramicrosoft-tutorial/tutorial_singlesign-onforjira_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<domain:port>/`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Abban az esetben azt egy elnevezett URL-cím, port nem kötelező. Ezek az értékek fogadásának Jira beépülő modul, az oktatóanyag későbbi részében ismertetett konfigurálása során.

5. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** , és illessze be a Jegyzettömbbe.

    ![A tanúsítvány letöltési hivatkozás](./media/jiramicrosoft-tutorial/tutorial_metadataurl.png) 

6. Egy másik böngészőablakban jelentkezzen be a JIRA-példány rendszergazdaként.

7. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **bővítmények**.

    ![Egyszeri bejelentkezés konfigurálása](./media/jiramicrosoft-tutorial/addon1.png)

8. Töltse le a beépülő modult a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56506). A beépülő modul használatával a Microsoft által biztosított feltöltött **bővítmény feltöltése** menü. A beépülő modul letöltése hatálya alá tartozó [Microsoft szolgáltatási szerződése](https://www.microsoft.com/servicesagreement/).

    ![Egyszeri bejelentkezés konfigurálása](./media/jiramicrosoft-tutorial/addon12.png)

9. A beépülő modul telepítése után megjelenik a **felhasználó telepített** bővítmények szakaszában **kiegészítő szolgáltatás kezelése** szakaszban. Kattintson a **konfigurálása** a új beépülő modul konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/jiramicrosoft-tutorial/addon13.png)

10. Hajtsa végre a következő lépéseket a konfigurációs lapon:

    ![Egyszeri bejelentkezés konfigurálása](./media/jiramicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Győződjön meg arról, hogy nincs-e rendelve az alkalmazást, hogy nincs hiba feloldása a metaadatokat a csak egy tanúsítványt. Ha több tanúsítványnak, a metaadatok feloldása után a rendszergazda lekérdezi hiba.

    a. Az a **metaadatok URL-címe** szövegmezőjébe illessze be **alkalmazás összevonási metaadatainak URL-címe** érték, amely az Azure Portalon, majd kattintson a másolta a **megoldásához** gombra. Ez olvassa be az identitásszolgáltató metaadatok URL-címet, és tölti fel a mezők adatai.

    b. Másolás a **azonosítóját, a válasz URL-cím és a bejelentkezési URL** értéket, majd illessze be őket a **azonosítóját, a válasz URL-cím és a bejelentkezési URL** rendre a szövegmezőből **JIRA SAML egyszeri bejelentkezés Microsoft-Domain és URL-címek** szakaszban az Azure Portalon.

    c. A **bejelentkezési gomb neve** írja be a szervezet azt szeretné, tekintse meg a bejelentkezési képernyőn a felhasználók gomb felirata.

    d. A **SAML felhasználói azonosító helyek** válassza **felhasználói Azonosítóját a tulajdonos utasítás NameIdentifier elemében van** vagy **felhasználói azonosító szerepel egy attribútumelem**.  Ez az azonosító nem lehet a JIRA felhasználói azonosító. Ha a felhasználói azonosító nem egyezik, majd rendszer nem engedélyezi felhasználók bejelentkezésének.

    > [!Note]
    > Alapértelmezett SAML Felhasználóazonosító helye alkalmazásnév-azonosító. Egy attribútum beállítást módosíthatja, és adja meg a megfelelő attribútum nevét.

    e. Ha **felhasználói azonosító szerepel egy attribútumelem** lehetőség, ezt a **attribútum neve** szövegmezőbe írja be a nevét, ahol a felhasználói azonosító várható.

    f. Ha az összevont tartományt (például AD FS stb.) az Azure ad-vel használ, majd kattintson a a **engedélyezése a Kezdőtartomány felderítésének** lehetőséget, majd konfigurálja a **tartománynév**.

    g. A **tartománynév** írja be a tartomány nevét itt az AD FS-alapú bejelentkezés esetén.

    h. Ellenőrizze **meg az egyszeri bejelentkezés engedélyezése** pedig jelentkezzen ki az Azure ad-ben, amikor egy felhasználó bejelentkezik a JIRA kíván.

    i. Kattintson a **mentése** gombra kattintva mentse a beállításokat.

    > [!NOTE]
    > Telepítési és hibaelhárítási kapcsolatos további információkért látogasson el [MS JIRA SSO összekötő rendszergazdai útmutató](../ms-confluence-jira-plugin-adminguide.md) , és nincs is [– gyakori kérdések](../ms-confluence-jira-plugin-faq.md) a segítségért

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_02.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="creating-jira-saml-sso-by-microsoft-test-user"></a>JIRA SAML egyszeri bejelentkezés a Microsoft a teszt felhasználó létrehozása

JIRA helyszíni kiszolgálón jelentkezzen be az Azure AD-felhasználók engedélyezéséhez, ki kell építeni JIRA SAML SSO, a Microsoft által. JIRA-SAML egyszeri bejelentkezés a Microsoft a kiépítés a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a JIRA helyileg működtetett kiszolgálón.

2. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **felhasználókezelés**.

    ![Alkalmazott hozzáadása](./media/jiramicrosoft-tutorial/user1.png)

3. Rendszergazdai hozzáférés lapon adja meg a rendszer átirányítja **jelszó** kattintson **megerősítése** gombra.

    ![Alkalmazott hozzáadása](./media/jiramicrosoft-tutorial/user2.png)

4. A **felhasználókezelés** szakasz lapra, majd **felhasználó létrehozása**.

    ![Alkalmazott hozzáadása](./media/jiramicrosoft-tutorial/user3.png) 

5. Az a **"Új felhasználó létrehozása"** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/jiramicrosoft-tutorial/user4.png) 

    a. Az a **E-mail-cím** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

    b. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be a felhasználó például Britta Simon teljes neve.

    c. Az a **felhasználónév** szövegmezőbe írja be az e-mailt, felhasználó, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    e. Kattintson a **felhasználó létrehozása**.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés JIRA SAML egyszeri bejelentkezés a Microsoft Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **JIRA SAML SSO Microsoft**.

    ![Egyszeri bejelentkezés konfigurálása](./media/jiramicrosoft-tutorial/tutorial_singlesign-onforjira_app.png)

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Által a hozzáférési panelen Microsoft csempére kattintva a JIRA SAML SSO, meg kell lekérése automatikusan bejelentkezett a JIRA SAML SSO a Microsoft-alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
