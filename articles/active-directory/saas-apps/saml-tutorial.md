---
title: 'Oktatóanyag: SAML 1.1-es tokent az Azure Active Directory integrálása engedélyezve LOB-alkalmazás |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory között, és a SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: edabc09f820093d088ec0b8ed1222fb26c800bee
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426428"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Oktatóanyag: SAML 1.1-es tokent az Azure Active Directory integrálása engedélyezve LOB-alkalmazás

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás az Azure Active Directoryval (Azure AD).

Integrálja a SAML 1.1-es jogkivonat engedélyezett LOB-alkalmazás az Azure ad-vel nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy az Azure AD ki férhet hozzá a SAML 1.1-es Token LOB-alkalmazás engedélyezve van.
- Engedélyezheti a felhasználók automatikusan el bejelentkezett a SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálhatja az Azure AD-integráció a SAML 1.1-es Token LOB-alkalmazás engedélyezve van, a következőkre van szüksége:

- Az Azure AD-előfizetéshez
- A SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás egyszeri bejelentkezési engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. SAML 1.1-jogkivonat hozzáadása engedélyezve van a LOB-alkalmazás a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>SAML 1.1-jogkivonat hozzáadása engedélyezve van a LOB-alkalmazás a katalógusból
SAML 1.1-es Token integrációjának konfigurálása engedélyezve LOB-alkalmazás az Azure AD-be, a SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás a galériából a felügyelt SaaS-alkalmazások listájában, hozzá kell adnia.

**SAML 1.1-es jogkivonat engedélyezve van a katalógusból LOB-alkalmazás hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás**, jelölje be **SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás a találatok listájában](./media/saml-tutorial/tutorial_saml_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a SAML 1.1-es jogkivonat engedélyezett LOB-alkalmazás "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, mely a partner felhasználót a SAML 1.1-es jogkivonat engedélyezett LOB-alkalmazás, hogy egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a SAML 1.1-es Token hivatkozás kapcsolata engedélyezve LOB-alkalmazás kell létrehozni.

A konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a SAML 1.1-es jogkivonat engedélyezve van a LOB-alkalmazáshoz, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás tesztfelhasználó](#create-a-saml-11-token-enabled-lob-app-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon a SAML 1.1-es jogkivonat engedélyezve van a LOB-alkalmazás, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés az Azure Portalon engedélyezése és konfigurálása egyszeri bejelentkezéshez a SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazáshoz alkalmazást.

**Konfigurálhatja az Azure AD egyszeri bejelentkezés a SAML 1.1-es jogkivonat engedélyezve van a LOB-alkalmazáshoz, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/saml-tutorial/tutorial_saml_samlbase.png)

1. Az a **SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás tartomány és URL-címek egyetlen bejelentkezési adatait](./media/saml-tutorial/tutorial_saml_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://your-app-url`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-címe: `https://your-app-url`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Alkalmazás konkrét URL-címekkel rendelkező, cserélje le ezeket az értékeket.  

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/saml-tutorial/tutorial_saml_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/saml-tutorial/tutorial_general_400.png)
    
1. Az a **SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazások konfigurálása** területén kattintson **SAML 1.1-es Token konfigurálása engedélyezve LOB-alkalmazás** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazások konfigurálása](./media/saml-tutorial/tutorial_saml_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás** oldalon kell küldenie a letöltött **tanúsítvány (Base64), kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** , alkalmazás az ügyfélszolgálathoz. Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/saml-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/saml-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/saml-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/saml-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-saml-11-token-enabled-lob-app-test-user"></a>Hozzon létre egy SAML 1.1-es jogkivonat engedélyezve van a tesztfelhasználó LOB-alkalmazás

Ebben a szakaszban hoz létre egy nevű Britta Simon felhasználó SAML 1.1-es Token LOB-alkalmazás engedélyezve. Az alkalmazás használata támogatási csoportjának felhasználó létrehozásához az alkalmazás oldalán. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon használható az Azure engedélyezi az egyszeri bejelentkezés a hozzáférés biztosításával SAML 1.1-es jogkivonat engedélyezve van a LOB-alkalmazás.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése a SAML 1.1-es jogkivonat engedélyezve van a LOB-alkalmazáshoz, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás**.

    ![A SAML 1.1-es jogkivonat engedélyezve van az alkalmazások listáját a LOB-alkalmazás-hivatkozás](./media/saml-tutorial/tutorial_saml_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Kattintás után a SAML 1.1-es jogkivonat engedélyezve LOB-alkalmazás csempe a hozzáférési panelen, a kapja meg automatikusan bejelentkezett, a SAML 1.1-es jogkivonat-kompatiblis alkalmazásokhoz LOB-alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saml-tutorial/tutorial_general_01.png
[2]: ./media/saml-tutorial/tutorial_general_02.png
[3]: ./media/saml-tutorial/tutorial_general_03.png
[4]: ./media/saml-tutorial/tutorial_general_04.png

[100]: ./media/saml-tutorial/tutorial_general_100.png

[200]: ./media/saml-tutorial/tutorial_general_200.png
[201]: ./media/saml-tutorial/tutorial_general_201.png
[202]: ./media/saml-tutorial/tutorial_general_202.png
[203]: ./media/saml-tutorial/tutorial_general_203.png
