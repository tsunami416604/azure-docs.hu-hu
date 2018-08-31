---
title: 'Oktatóanyag: Azure Active Directory-integráció az ArcGIS vállalati |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az ArcGIS Enterprise között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: jeedes
ms.openlocfilehash: ea2b32b43fedacba7b8a60db29762c32fda65aa5
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306342"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Oktatóanyag: Azure Active Directory-integráció az ArcGIS Enterprise

Ebben az oktatóanyagban elsajátíthatja, hogyan ArcGIS vállalati integrálása az Azure Active Directory (Azure AD).

Az ArcGIS vállalati integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá az ArcGIS vállalati Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett ArcGIS Enterprise (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az ArcGIS Enterprise, a következőkre van szükség:

- Azure AD-előfizetés
- Az ArcGIS vállalati egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Az ArcGIS vállalati hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Az ArcGIS vállalati hozzáadása a katalógusból

Az Azure AD-be az ArcGIS vállalati integráció konfigurálásához, hozzá kell ArcGIS vállalati a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Az ArcGIS vállalati hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **ArcGIS vállalati**, jelölje be **ArcGIS vállalati** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában ArcGIS Enterprise](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és az Azure AD egyszeri bejelentkezés tesztelése az ArcGIS Enterprise "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó ArcGIS vállalati mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó ArcGIS vállalati hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az ArcGIS Enterprise tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy ArcGIS vállalati tesztfelhasználót](#create-an-arcgis-enterprise-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon ArcGIS vállalat, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az ArcGIS vállalati alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása az ArcGIS Enterprise, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **ArcGIS vállalati** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_samlbase.png)

3. Az a **ArcGIS vállalati tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Az ArcGIS vállalati tartomány és URL-címek egyszeri bejelentkezési adatait](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url1.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `<EXTERNAL_DNS_NAME>.portal`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

4. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Az ArcGIS vállalati tartomány és URL-címek egyszeri bejelentkezési adatait](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url2.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [ArcGIS vállalati ügyfél-támogatási csapatának](mailto:support@esri.com) beolvasni ezeket az értékeket. Az azonosító értéket kap **identitásszolgáltató beállítása** szakaszban, az oktatóanyag későbbi részében ismertetett.

5. Az a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** , és illessze be a Jegyzettömbbe.

    ![A tanúsítvány letöltési hivatkozás](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_certificate.png)

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/arcgisenterprise-tutorial/tutorial_general_400.png)

7. Egy másik böngészőablakban jelentkezzen be az ArcGIS vállalati vállalati hely rendszergazdaként.

8. Válassza ki **szervezet > Beállítások szerkesztése**.

    ![Az ArcGIS nagyvállalati konfiguráció](./media/arcgisenterprise-tutorial/configure1.png)

9. Válassza ki **biztonsági** fülre.

    ![Az ArcGIS nagyvállalati konfiguráció](./media/arcgisenterprise-tutorial/configure2.png)

10. Görgessen le a **SAML vállalati bejelentkezést** szakaszt, és válassza **állítsa be a vállalati bejelentkezési**.

    ![Az ArcGIS nagyvállalati konfiguráció](./media/arcgisenterprise-tutorial/configure3.png)

11. Az a **identitásszolgáltató beállítása** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Az ArcGIS nagyvállalati konfiguráció](./media/arcgisenterprise-tutorial/configure4.png)

    a. Adjon meg egy nevet, például **Azure Active Directory teszt** a a **neve** szövegmezőbe.

    b. Az a **URL-cím** szövegmezőjébe illessze be a **alkalmazás összevonási metaadatainak URL-címe** az Azure Portalról másolt érték.

    c. Kattintson a **speciális beállítások megjelenítése** , és másolja a **Entitásazonosító** értékét, és illessze be azt a **azonosító** szövegmezőjébe a **ArcGIS vállalati tartomány és URL-címek** szakaszban az Azure Portalon.
    
    ![Az ArcGIS nagyvállalati konfiguráció](./media/arcgisenterprise-tutorial/configure5.png)

    d. Kattintson a **frissítés IDENTITÁSSZOLGÁLTATÓ**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/arcgisenterprise-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/arcgisenterprise-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/arcgisenterprise-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/arcgisenterprise-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-an-arcgis-enterprise-test-user"></a>Hozzon létre egy ArcGIS vállalati tesztfelhasználó számára

Ez a szakasz célja egy ArcGIS vállalati Britta Simon nevű felhasználó létrehozásához. ArcGIS vállalati támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó próbál hozzáférni a vállalati ArcGIS, ha még nem létezik jön létre.

> [!Note]
> Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [ArcGIS vállalati támogatási csapatának](mailto:support@esri.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított ArcGIS vállalati hozzáférés Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel az ArcGIS Enterprise, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **ArcGIS vállalati**.

    ![Az ArcGIS vállalati hivatkozásra az alkalmazások listáját](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a ArcGIS vállalati csempére kattint, meg kell lekérése automatikusan bejelentkezett az ArcGIS vállalati alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/arcgisenterprise-tutorial/tutorial_general_01.png
[2]: ./media/arcgisenterprise-tutorial/tutorial_general_02.png
[3]: ./media/arcgisenterprise-tutorial/tutorial_general_03.png
[4]: ./media/arcgisenterprise-tutorial/tutorial_general_04.png

[100]: ./media/arcgisenterprise-tutorial/tutorial_general_100.png

[200]: ./media/arcgisenterprise-tutorial/tutorial_general_200.png
[201]: ./media/arcgisenterprise-tutorial/tutorial_general_201.png
[202]: ./media/arcgisenterprise-tutorial/tutorial_general_202.png
[203]: ./media/arcgisenterprise-tutorial/tutorial_general_203.png