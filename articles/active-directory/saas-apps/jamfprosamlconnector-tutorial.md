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
ms.date: 07/27/2018
ms.author: jeedes
ms.openlocfilehash: 94b8b935728110cd5dd07b2066e8320274e3b082
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428417"
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

- Az Azure AD-előfizetéshez
- A Jamf Pro egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A Jamf Pro hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-jamf-pro-from-the-gallery"></a>A Jamf Pro hozzáadása a katalógusból
A Jamf Pro integrálása az Azure AD beállítása, hozzá kell a Jamf Pro a galériából a felügyelt SaaS-alkalmazások listájára.

**A Jamf Pro hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **a Jamf Pro**, jelölje be **a Jamf Pro** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A Jamf Pro a találatok listájában](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és az Azure AD egyszeri bejelentkezés tesztelése a Jamf Pro "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó a Jamf Pro szolgáltatásban mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a Jamf Pro szolgáltatásban a kapcsolódó felhasználó hivatkozás kapcsolatát kell létrehozni.

Az Azure AD egyszeri bejelentkezés vizsgálata a Jamf Pro konfigurálni, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[A Jamf Pro tesztfelhasználó létrehozása](#create-a-jamf-pro-test-user)**  - a-megfelelője a Britta Simon rendelkezik a Jamf Pro szolgáltatásban, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Jamf Pro alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása a Jamf Pro, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **a Jamf Pro** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

1. Az a **a Jamf Pro tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![A Jamf Pro tartomány és URL-címek egyszeri bejelentkezési adatait](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![A Jamf Pro tartomány és URL-címek egyszeri bejelentkezési adatait](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.jamfcloud.com`
     
    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. A tényleges azonosító értéket kap **egyszeri bejelentkezés** a Jamf Pro portál, az oktatóanyag későbbi részében ismertetett szakaszban. A tényleges kibonthatja **altartomány** értéket az azonosító értékét, és azt használja **altartomány** adatokat bejelentkezési URL- és a válasz URL-cím.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** , és illessze be a Jegyzettömbbe.

    ![A tanúsítvány letöltési hivatkozás](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
1. Egy másik böngészőablakban jelentkezzen be a Jamf Pro vállalati hely rendszergazdaként.

1. Kattintson a **beállítások ikon** , az oldal jobb felső sarkában.

    ![A Jamf Pro-konfiguráció](./media/jamfprosamlconnector-tutorial/configure1.png)

1. Kattintson a **egyszeri bejelentkezés**.

    ![A Jamf Pro-konfiguráció](./media/jamfprosamlconnector-tutorial/configure2.png)

1. Az a **egyszeri bejelentkezés** oldalon tegye a következőket:

    ![A Jamf Pro egyetlen](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Válassza ki **a Jamf Pro Server** egyszeri bejelentkezéses hozzáférés engedélyezéséhez.

    b. Kiválasztásával **az összes felhasználó engedélyezése Mellőzés** a felhasználók nem irányítja át az identitásszolgáltató bejelentkezési oldal a hitelesítéshez, de is bejelentkezhetnek a Jamf Pro közvetlenül helyette. Amikor egy felhasználó megpróbál hozzáférni a Jamf Pro az identitásszolgáltató-n keresztül, identitásszolgáltató által kezdeményezett egyszeri Bejelentkezést, hitelesítést és engedélyezést történik.

    c. Válassza ki a **NameID** beállításhoz tartozó **FELHASZNÁLÓLEKÉPEZÉS: SAML**. Alapértelmezés szerint ez a beállítás **NameID** , de előfordulhat, hogy megadhat egy egyéni attribútumot.

    d. Válassza ki **E-mail** a **felhasználó-HOZZÁRENDELÉS: a JAMF PRO**. A Jamf Pro leképezi a következő módokon az identitásszolgáltató által küldött SAML-attribútumok: a felhasználók és csoportok számára. Amikor egy felhasználó megpróbál hozzáférni a Jamf Pro a Jamf Pro alapértelmezés szerint a felhasználóval kapcsolatos adatok beolvasása az identitásszolgáltató, és egyeztet a Jamf Pro felhasználói fiókok ellen. Ha a bejövő felhasználói fiók nem létezik a Jamf Pro szolgáltatásban, majd csoport névegyeztetés történik.

    e. Illessze be az értéket `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` a a **ATTRIBÚTUM CSOPORTNÉV** szövegmezőbe.
 
1. A legfeljebb azonos lapon görgetve **IDENTITÁSSZOLGÁLTATÓ** alatt a **egyszeri bejelentkezés** szakaszt, és hajtsa végre az alábbi lépéseket:

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

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/jamfprosamlconnector-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/jamfprosamlconnector-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/jamfprosamlconnector-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-jamf-pro-test-user"></a>A Jamf Pro tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a Jamf Pro, azok ki kell építeni a Jamf Pro be. Esetén a Jamf Pro oldalán kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Jamf Pro vállalati webhely.

1. Kattintson a **beállítások ikon** , az oldal jobb felső sarkában.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/configure1.png)

1. Kattintson a **a Jamf Pro felhasználói fiókok és csoportok**.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user1.png)

1. Kattintson az **Új** lehetőségre.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user2.png)

1. Válassza ki **standard szintű fiók létrehozása**.

    ![Alkalmazott hozzáadása](./media/jamfprosamlconnector-tutorial/user3.png)

1. Az a **új fiók** dailog, hajtsa végre az alábbi lépéseket:

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

![A felhasználói szerepkör hozzárendelése][200] 

**A Jamf Pro Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **a Jamf Pro**.

    ![A Jamf Pro hivatkozásra az alkalmazások listáját](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Jamf Pro csempére kattint, akkor kell lekérése automatikusan bejelentkezett a Jamf Pro alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/jamfprosamlconnector-tutorial/tutorial_general_203.png

