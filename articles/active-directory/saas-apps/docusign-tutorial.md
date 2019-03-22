---
title: 'Oktatóanyag: Az Azure Active Directory-integráció, a DocuSign |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a DocuSign között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fda9df8e7781a9e0c45fb1aead9f8167f89a833
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850870"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Oktatóanyag: A DocuSign az Azure Active Directory-integráció

Ebben az oktatóanyagban megismerheti, hogyan integrálható a DocuSign az Azure Active Directory (Azure AD).

Az Azure AD integrálása a DocuSign nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a DocuSign az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett, a DocuSign (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a DocuSign, a következőkre van szükség:

- Azure AD-előfizetés
- A DocuSign az egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadás a katalógusból, DocuSign
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-docusign-from-the-gallery"></a>Hozzáadás a katalógusból, DocuSign

Konfigurálhatja az Azure AD integrálása a DocuSign, hozzá kell DocuSign a galériából a felügyelt SaaS-alkalmazások listájára.

**DocuSign hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **DocuSign**válassza **DocuSign** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![DocuSign, a találatok listájában](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés DocuSign, a teszt "Britta Simon" nevű felhasználó.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a DocuSign tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Docusignban hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a DocuSign tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[DocuSign tesztfelhasználó létrehozása](#creating-a-docusign-test-user)**  – van egy Britta Simon megfelelője a DocuSign, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és DocuSign-alkalmazását az egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés, a DocuSign, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **DocuSign** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial_general_301.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![DocuSign-tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/docusign-tutorial/tutorial_docusign_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges bejelentkezési URL-és a később ismertetett azonosító **SAML 2.0-s végpontok megtekintése** rész a jelen oktatóanyagban.

5. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **tanúsítvány (Base64)**, és mentse a tanúsítványfájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

6. Az a **állítsa be a DocuSign** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

    ![DocuSign-konfiguráció](common/configuresection.png)

7. Egy másik böngészőablakban, jelentkezzen be a **DocuSign felügyeleti portál** rendszergazdaként.

8. A felső a lap jobb oldalán kattintson a profil **embléma** majd kattintson a **nyissa meg rendszergazda**.
  
    ![Egyszeri bejelentkezés konfigurálása][51]

9. A tartomány megoldások lapon kattintson a **tartományok**

    ![Egyszeri bejelentkezés konfigurálása][50]

10. Alatt a **tartományok** területén kattintson **JOGCÍM tartomány**.

    ![Egyszeri bejelentkezés konfigurálása][52]

11. Az a **jogcím egy tartomány** párbeszédpanelen, a a **tartománynév** szövegmezőbe írja be a vállalati tartományhoz, és kattintson **JOGCÍM**. Győződjön meg arról, hogy a tartomány ellenőrzéséhez és az állapot aktív.

    ![Egyszeri bejelentkezés konfigurálása][53]

12. Kattintson a tartomány megoldások lap **Identitásszolgáltatók**.
  
    ![Egyszeri bejelentkezés konfigurálása][54]

13. A **Identitásszolgáltatók** területén kattintson **IDENTITÁSSZOLGÁLTATÓ hozzáadása**. 

    ![Egyszeri bejelentkezés konfigurálása][55]

14. Az a **identitás Szolgáltatóbeállítások** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása][56]

    a. Az a **neve** szövegmezőbe írja be egy egyedi nevet a konfigurációnak. Ne használjon szóközöket.

    b. Az a **identitásszolgáltató kibocsátója szövegmező**, illessze be az értéket a **az Azure AD-azonosító**, az Azure Portalról másolt.

    c. Az a **Identity Provider bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    d. A a **Identity Provider kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket, **kijelentkezési URL-címe**, az Azure Portalról másolt.

    e. Válassza ki **AuthN bejelentkezési kérelem**.

    f. Mint **kérelmének küldése AuthN**válassza **POST**.

    g. Mint **küldési kijelentkezési kérés által**válassza **első**.

    h. Az a **egyéni attribútumleképzés** területén kattintson a **új HOZZÁRENDELÉS hozzáadása**.

    ![Egyszeri bejelentkezés konfigurálása][62]

    i. Válassza ki a mezőt hozzá kívánja rendelni az Azure AD jogcímet. Ebben a példában a **emailaddress** jogcím van leképezve a következő értékkel: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Az alapértelmezett jogcím név, e-mail követelés, majd kattintson az Azure AD-ből **mentése**.

    ![Egyszeri bejelentkezés konfigurálása][57]

    > [!NOTE]
    > Használja a megfelelő **felhasználóazonosító** hozzárendelni a felhasználó Azure AD-ből, a DocuSign felhasználó-hozzárendelés. Válassza ki a megfelelő mezőt, és adja meg a megfelelő értéket, a szervezet beállítások alapján.

    j. Az a **identitás szolgáltatói tanúsítványok** területén kattintson **tanúsítvány hozzáadása**, majd töltse fel az Azure AD-portálról letöltött, majd kattintson a tanúsítvány **mentése**.

    ![Egyszeri bejelentkezés konfigurálása][58]

    k. Az a **Identitásszolgáltatók** területén kattintson **műveletek**, és kattintson a **végpontok**.

    ![Egyszeri bejelentkezés konfigurálása][59]

    l. Az a **SAML 2.0-s végpontok megtekintése** szakaszában **DocuSign felügyeleti portálon**, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása][60]

    * Másolás a **szolgáltató kibocsátó URL-címe**, és illessze be azt a **azonosító** szövegmezőjébe **DocuSign-tartomány és URL-címek** szakaszban az Azure Portalon.

    * Másolás a **szolgáltató bejelentkezési URL-címe**, és illessze be azt a **bejelentkezési URL-** szövegmezőjébe **DocuSign-tartomány és URL-címek** szakaszban az Azure Portalon.

    * Kattintson a **bezárása**

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_02.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon\@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="creating-a-docusign-test-user"></a>DocuSign tesztfelhasználó létrehozása

Ez a szakasz célja, a DocuSign Britta Simon nevű felhasználó létrehozásához. DocuSign támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó próbál hozzáférni a DocuSign, ha még nem létezik jön létre.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [DocuSign-ügyfélszolgálathoz](https://support.docusign.com/).

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon DocuSign, a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **DocuSign**.

    ![Egyszeri bejelentkezés konfigurálása](./media/docusign-tutorial/tutorial_docusign_app.png)

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a DocuSign csempére kattint, meg kell lekérése automatikusan bejelentkezett a DocuSign-alkalmazásba.
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
[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
