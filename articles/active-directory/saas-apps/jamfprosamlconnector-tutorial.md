---
title: 'Oktatóanyag: Azure Active Directory-integráció a Jamf Pro |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Jamf Pro között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: d28e28a2c4f8144da16c4838f07c9b8bb5ce67f0
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268157"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Oktatóanyag: Azure Active Directory-integráció a Jamf Pro

Ebben az oktatóanyagban elsajátíthatja Jamf Pro integrálása az Azure Active Directory (Azure AD).

A Jamf Pro integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Jamf Pro Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Jamf Pro (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Jamf Pro, a következőkre van szükség:

- Azure AD-előfizetés
- A Jamf Pro egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A Jamf Pro hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-jamf-pro-from-the-gallery"></a>A Jamf Pro hozzáadása a katalógusból

A Jamf Pro integrálása az Azure AD beállítása, hozzá kell a Jamf Pro a galériából a felügyelt SaaS-alkalmazások listájára.

**A Jamf Pro hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![image](./media/jamfprosamlconnector-tutorial/selectazuread.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![image](./media/jamfprosamlconnector-tutorial/a_select_app.png)
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![image](./media/jamfprosamlconnector-tutorial/a_new_app.png)

4. A Keresés mezőbe írja be a **a Jamf Pro**, jelölje be **a Jamf Pro** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![image](./media/jamfprosamlconnector-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és az Azure AD egyszeri bejelentkezés tesztelése a Jamf Pro "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó a Jamf Pro szolgáltatásban mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a Jamf Pro szolgáltatásban a kapcsolódó felhasználó hivatkozás kapcsolatát kell létrehozni.

Az Azure AD egyszeri bejelentkezés vizsgálata a Jamf Pro konfigurálni, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[A Jamf Pro tesztfelhasználó létrehozása](#create-a-jamf-pro-test-user)**  - a-megfelelője a Britta Simon rendelkezik a Jamf Pro szolgáltatásban, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Jamf Pro alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása a Jamf Pro, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com/), a a **a Jamf Pro** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![image](./media/jamfprosamlconnector-tutorial/b1_b2_select_sso.png)

2. Kattintson a **módosítása egyszeri bejelentkezési mód** felett válassza ki a képernyő a **SAML** mód.

      ![image](./media/jamfprosamlconnector-tutorial/b1_b2_saml_ssso.png)

3. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![image](./media/jamfprosamlconnector-tutorial/b1_b2_saml_sso.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![image](./media/jamfprosamlconnector-tutorial/b1-domains_and_urlsedit.png)

5. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    a. Az a **azonosító** szövegmezőbe írja be egy URL-címet a következő mintával: `https://<subdomain>.jamfcloud.com/saml/metadata`.

    b. Az a **válasz URL-cím** szövegmezőbe írja be egy URL-címet a következő mintával: `https://<subdomain>.jamfcloud.com/saml/SSO`.

    ![image](./media/jamfprosamlconnector-tutorial//b2-domains_and_urls.png)

    c. Kattintson a **további URL-címet beállítani**.

    d. Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címet a következő mintával: `https://<subdomain>.jamfcloud.com`.

    ![image](./media/jamfprosamlconnector-tutorial//b4-domains_and_urls.png)

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. A tényleges azonosító értéket kap **egyszeri bejelentkezés** a Jamf Pro portál, az oktatóanyag későbbi részében ismertetett szakaszban. A tényleges kibonthatja **altartomány** értéket az azonosító értékét, és azt használja **altartomány** adatokat bejelentkezési URL- és a válasz URL-cím.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** és mentse a számítógép.

    ![image](./media/jamfprosamlconnector-tutorial/C2_certificate.png)

7. Automatizálhatja a Jamf Pro konfigurációra, telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![image](./media/jamfprosamlconnector-tutorial/install_extension.png)
 
8. A felvett bővítmény a böngészőre, kattintson a **beállítása a Jamf Pro** fog irányítja át a Jamf Pro alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatait, jelentkezzen be a Jamf Pro szolgáltatásban. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és 9 és 12 lépések automatizálásához.

    ![image](./media/jamfprosamlconnector-tutorial/d1_saml.png)

9. Ha szeretné manuálisan beállítani a Jamf Pro, nyissa meg egy új böngészőablakban, és jelentkezzen be a Jamf Pro vállalati hely rendszergazdaként, és hajtsa végre az alábbi lépéseket:

10. Kattintson a **beállítások ikon** , az oldal jobb felső sarkában.

    ![A Jamf Pro-konfiguráció](./media/jamfprosamlconnector-tutorial/configure1.png)

11. Kattintson a **egyszeri bejelentkezés**.

    ![A Jamf Pro-konfiguráció](./media/jamfprosamlconnector-tutorial/configure2.png)

12. Az a **egyszeri bejelentkezés** oldalon tegye a következőket:

    ![A Jamf Pro egyetlen](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Válassza ki **a Jamf Pro Server** egyszeri bejelentkezéses hozzáférés engedélyezéséhez.

    b. Kiválasztásával **az összes felhasználó engedélyezése Mellőzés** a felhasználók nem irányítja át az identitásszolgáltató bejelentkezési oldal a hitelesítéshez, de is bejelentkezhetnek a Jamf Pro közvetlenül helyette. Amikor egy felhasználó megpróbál hozzáférni a Jamf Pro az identitásszolgáltató-n keresztül, identitásszolgáltató által kezdeményezett egyszeri Bejelentkezést, hitelesítést és engedélyezést történik.

    c. Válassza ki a **NameID** beállításhoz tartozó **FELHASZNÁLÓLEKÉPEZÉS: SAML**. Alapértelmezés szerint ez a beállítás **NameID** , de előfordulhat, hogy megadhat egy egyéni attribútumot.

    d. Válassza ki **E-mail** a **felhasználó-HOZZÁRENDELÉS: a JAMF PRO**. A Jamf Pro leképezi a következő módokon az identitásszolgáltató által küldött SAML-attribútumok: a felhasználók és csoportok számára. Amikor egy felhasználó megpróbál hozzáférni a Jamf Pro a Jamf Pro alapértelmezés szerint a felhasználóval kapcsolatos adatok beolvasása az identitásszolgáltató, és egyeztet a Jamf Pro felhasználói fiókok ellen. Ha a bejövő felhasználói fiók nem létezik a Jamf Pro szolgáltatásban, majd csoport névegyeztetés történik.

    e. Illessze be az értéket `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` a a **ATTRIBÚTUM CSOPORTNÉV** szövegmezőbe.

13. A legfeljebb azonos lapon görgetve **IDENTITÁSSZOLGÁLTATÓ** alatt a **egyszeri bejelentkezés** szakaszt, és hajtsa végre az alábbi lépéseket:

    ![A Jamf Pro-konfiguráció](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Válassza ki **más** a lehetőségként a **IDENTITÁSSZOLGÁLTATÓ** legördülő listából.

    b. Az a **egyéb SZOLGÁLTATÓ** szövegmezőbe írja be **Azure ad-ben**.

    c. Válassza ki **metaadatok URL-címe** lehetőségként a a **IDENTITY PROVIDER METAADATFORRÁS** legördülő listából, majd illessze be az alábbi szövegmezőbe a **alkalmazás összevonási metaadatainak URL-címe** érték, amely az Azure Portalról másolta.

    d. Másolás a **Entitásazonosító** értékét, és illessze be azt a **azonosító (entityid)** szövegmezőjébe **a Jamf Pro tartomány és URL-címek** szakaszban az Azure Portalon.

    >[!NOTE]
    > Itt elmosódott értéke az altartomány része. Ez az érték használatával végezze el a bejelentkezési URL- és a válasz URL-cím a **a Jamf Pro tartomány és URL-címek** szakaszban az Azure Portalon.

    e. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![image](./media/jamfprosamlconnector-tutorial/d_users_and_groups.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![image](./media/jamfprosamlconnector-tutorial/d_adduser.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![image](./media/jamfprosamlconnector-tutorial/d_userproperties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="create-a-jamf-pro-test-user"></a>A Jamf Pro tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a Jamf Pro, azok ki kell építeni a Jamf Pro be. Esetén a Jamf Pro oldalán kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Jamf Pro vállalati webhely.

2. Kattintson a **beállítások ikon** , az oldal jobb felső sarkában.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Kattintson a **a Jamf Pro felhasználói fiókok és csoportok**.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user1.png)

4. Kattintson az **Új** lehetőségre.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user2.png)

5. Válassza ki **standard szintű fiók létrehozása**.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user3.png)

6. Az a **új fiók** dailog, hajtsa végre az alábbi lépéseket:

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Az a **felhasználónév** szövegmezőbe írja be a BrittaSimon teljes nevét.

    b. Válassza ki a megfelelő beállításokat a szervezet megfelelően **hozzáférési szint**, **jogosultság beállítása**, és a **hozzáférési állapota**.

    c. Az a **teljes FÁJLVISSZAÁLLÍTÁSI név** szövegmezőbe írja be a Britta Simon teljes nevét.

    d. Az a **E-mail cím** szövegmezőbe írja be a Britta Simon fiók e-mail-címét.

    e. Az a **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    f. Az a **jelszó ellenőrzése** szövegmezőbe írja be a felhasználó jelszavát.

    g. Kattintson a **Save** (Mentés) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon hozzáférés biztosítása a Jamf Pro által használandó Azure egyszeri bejelentkezést.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **a Jamf Pro**.

    ![image](./media/jamfprosamlconnector-tutorial/d_all_applications.png)

2. Az alkalmazások listájában jelölje ki a **a Jamf Pro**.

    ![image](./media/jamfprosamlconnector-tutorial/d_all_proapplications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![image](./media/jamfprosamlconnector-tutorial/d_leftpaneusers.png)

4. Válassza ki a **Hozzáadás** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![image](./media/jamfprosamlconnector-tutorial/d_assign_user.png)

4. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

5. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Jamf Pro csempére kattint, akkor kell lekérése automatikusan bejelentkezett a Jamf Pro alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
