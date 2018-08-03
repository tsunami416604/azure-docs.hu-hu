---
title: 'Oktatóanyag: Azure Active Directory-integráció Skyhigh hálózatokkal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Skyhigh hálózatok között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 48d6ddd1-4d3e-4019-8234-5e5212684d9c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: jeedes
ms.openlocfilehash: 40237946adf0e9cf30367fd0464a6c32572c3aaf
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423725"
---
# <a name="tutorial-azure-active-directory-integration-with-skyhigh-networks"></a>Oktatóanyag: Azure Active Directory-integráció Skyhigh hálózatokkal

Ebben az oktatóanyagban elsajátíthatja, hogyan Skyhigh hálózatok integrálása az Azure Active Directory (Azure AD).

Skyhigh hálózatok integrálása az Azure AD-vel nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Skyhigh hálózatok az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Skyhigh hálózatokhoz (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Skyhigh hálózatokkal, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Skyhigh hálózatok egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Skyhigh hálózatok hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-skyhigh-networks-from-the-gallery"></a>Skyhigh hálózatok hozzáadása a katalógusból
Az Azure AD integrálása a Skyhigh hálózatok konfigurálásához, hozzá kell Skyhigh hálózatok a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Skyhigh hálózatok hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Skyhigh hálózatok**válassza **Skyhigh hálózatok** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Skyhigh hálózatok](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Skyhigh hálózatok egy "Britta Simon" nevű tesztelési felhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Skyhigh hálózatok mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Skyhigh hálózatok közötti kapcsolat kapcsolatot kell hozható létre.

Az Azure AD egyszeri bejelentkezés Skyhigh hálózatokkal tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Skyhigh hálózatok tesztfelhasználót](#create-a-skyhigh-networks-test-user)**  - a-megfelelője a Britta Simon rendelkezik Skyhigh hálózatokból, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Skyhigh hálózatok alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Skyhigh hálózatokkal, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Skyhigh hálózatok** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_samlbase.png)

1. Az a **Skyhigh hálózatok tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Skyhigh hálózatok tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_url.png)

    a. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Skyhigh hálózatok tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [Skyhigh hálózatok ügyfél-támogatási csapatának](mailto:support@skyhighnetworks.com) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/skyhighnetworks-tutorial/tutorial_general_400.png)
    
1. Az a **Skyhigh hálózatok konfigurációs** területén kattintson **Skyhigh hálózatok konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Skyhigh hálózatok konfigurálása](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **Skyhigh hálózatok** oldalon kell küldenie a letöltött **tanúsítvány (Base64), kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** való [ Skyhigh hálózatok támogatási csoportjának](mailto:support@skyhighnetworks.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/skyhighnetworks-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/skyhighnetworks-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/skyhighnetworks-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/skyhighnetworks-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-skyhigh-networks-test-user"></a>Skyhigh hálózatok tesztfelhasználó létrehozása

Ebben a szakaszban egy Skyhigh hálózatok Britta Simon nevű felhasználó hoz létre. Együttműködve [Skyhigh hálózatok támogatási csoportjának](mailto:support@skyhighnetworks.com) a felhasználók hozzáadása az Skyhigh hálózatok platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Skyhigh hálózatokhoz a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon Skyhigh hálózatokhoz való hozzárendeléséhez végezze el az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Skyhigh hálózatok**.

    ![Az alkalmazások listáját a Skyhigh hálózatok hivatkozás](./media/skyhighnetworks-tutorial/tutorial_skyhighnetworks_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Skyhigh hálózatok csempére kattint, meg kell lekérése automatikusan bejelentkezett az Skyhigh hálózatok alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skyhighnetworks-tutorial/tutorial_general_01.png
[2]: ./media/skyhighnetworks-tutorial/tutorial_general_02.png
[3]: ./media/skyhighnetworks-tutorial/tutorial_general_03.png
[4]: ./media/skyhighnetworks-tutorial/tutorial_general_04.png

[100]: ./media/skyhighnetworks-tutorial/tutorial_general_100.png

[200]: ./media/skyhighnetworks-tutorial/tutorial_general_200.png
[201]: ./media/skyhighnetworks-tutorial/tutorial_general_201.png
[202]: ./media/skyhighnetworks-tutorial/tutorial_general_202.png
[203]: ./media/skyhighnetworks-tutorial/tutorial_general_203.png

