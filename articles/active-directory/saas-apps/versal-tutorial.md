---
title: 'Oktatóanyag: Azure Active Directory-integráció az Zményes |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Zményes között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 5b2e53c0-61a3-4954-ae46-8c28c6368bfd
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeedes
ms.openlocfilehash: a6e1f73218efb11da475f3e67188863c3b99de97
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421338"
---
# <a name="tutorial-azure-active-directory-integration-with-versal"></a>Oktatóanyag: Azure Active Directory-integráció az bejárás

Ebben az oktatóanyagban elsajátíthatja, hogyan Zményes integrálása az Azure Active Directory (Azure AD).

Zményes integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Zményes Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Zményes (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Zményes az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Zményes egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Zményes hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-versal-from-the-gallery"></a>Zményes hozzáadása a katalógusból
Az Azure ad-ben Zményes integráció konfigurálásához, hozzá kell Zményes a galériából a felügyelt SaaS-alkalmazások listájára.

**Zményes hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Zményes**válassza **Zményes** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában bejárás](./media/versal-tutorial/tutorial_versal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Zményes a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Zményes mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Zményes hivatkozás kapcsolata kell létrehozni.

Zményes, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Zményes tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Zményes tesztfelhasználót](#create-a-versal-test-user)**  – egy megfelelője a Britta Simon Zményes, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Zményes alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Zményes, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Zményes** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/versal-tutorial/tutorial_versal_samlbase.png)

1. Az a **Zményes tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Versal tartomány és URL-címek egyszeri bejelentkezési adatait](./media/versal-tutorial/tutorial_versal_url.png)

    a. Az a **azonosító** szövegmezőbe írja be az értéket: `VERSAL`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://versal.com/sso/saml/orgs/<organization_id>`

    > [!NOTE] 
    > Válasz URL-cím értéke nem valódi. Ez az érték frissítse a tényleges válasz URL-cím. Kapcsolattartó [Zményes támogatási csapatának](https://support.versal.com/hc/) lekérni ezt az értéket.
    
1. Az alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőfelvételen látható erre egy példa látható. Az alapértelmezett érték **felhasználóazonosító** van **user.userprincipalname** , de **Zményes** vár ezt a képezhető le a felhasználó e-mail-címmel. Használhatja, amely **user.mail** attribútumot a listából, vagy használja a megfelelő attribútum értéket, a szervezet konfiguráció alapján.
    
    ![Felhasználói azonosító legördülő menü](./media/versal-tutorial/tutorial_versal_attribute.png)

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/versal-tutorial/tutorial_versal_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/versal-tutorial/tutorial_general_400.png)
    
1. Az egyszeri bejelentkezés konfigurálása **Zményes** oldalon kell küldenie a letöltött **metaadatainak XML** és **SAML-aláíró tanúsítvány** való [Zményes támogatási csoport ](https://support.versal.com/hc/). A vállalat konfigurálja a Zményes cégnek vagy szervezetnek a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/versal-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/versal-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/versal-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/versal-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-versal-test-user"></a>Zményes tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Zményes hoz létre. Kérjük, kövesse a [tesztfelhasználó létrehozása egy SAML](https://support.versal.com/hc/en-us/articles/115011672887-Creating-a-SAML-test-user) támogatási útmutató Britta Simon a szervezetben lévő felhasználó létrehozásához. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt Zményes aktiválva. 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Zményes Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Zményes, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Zményes**.

    ![Az alkalmazások listáját a Zményes hivatkozásra](./media/versal-tutorial/tutorial_versal_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a webhelyre beágyazva Zményes tanfolyam segítségével tesztelnie.
Tekintse át a [szervezeti tanfolyamok beágyazás](https://support.versal.com/hc/en-us/articles/203271866-Embedding-organizational-courses) **SAML egyszeri bejelentkezés** támogatása az útmutató utasításokat Zményes tanfolyam beágyazásával támogatásával az Azure ad egyszeri bejelentkezés. 

Szüksége lesz egy tanfolyam létrehozása, a szervezet többi tagjával is megoszthat és tanfolyam beágyazás teszteléséhez tegye közzé. Lásd: [tanfolyam létrehozása](https://support.versal.com/hc/en-us/articles/203722528-Create-a-course), [tanfolyam közzétételi](https://support.versal.com/hc/en-us/articles/203753398-Publishing-a-course), és [tanfolyam és learner felügyeleti](https://support.versal.com/hc/en-us/articles/206029467-Course-and-learner-management) további információt.  
                     

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/versal-tutorial/tutorial_general_01.png
[2]: ./media/versal-tutorial/tutorial_general_02.png
[3]: ./media/versal-tutorial/tutorial_general_03.png
[4]: ./media/versal-tutorial/tutorial_general_04.png

[100]: ./media/versal-tutorial/tutorial_general_100.png

[200]: ./media/versal-tutorial/tutorial_general_200.png
[201]: ./media/versal-tutorial/tutorial_general_201.png
[202]: ./media/versal-tutorial/tutorial_general_202.png
[203]: ./media/versal-tutorial/tutorial_general_203.png

