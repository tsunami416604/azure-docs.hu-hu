---
title: 'Oktatóanyag: Azure Active Directory-integráció az FilesAnywhere |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és FilesAnywhere között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: jeedes
ms.openlocfilehash: 8a08155dd67c6fcf2fb080325840bc163dc6da60
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447356"
---
# <a name="tutorial-azure-active-directory-integration-with-filesanywhere"></a>Oktatóanyag: Azure Active Directory-integráció az FilesAnywhere

Ebben az oktatóanyagban elsajátíthatja, hogyan FilesAnywhere integrálása az Azure Active Directory (Azure AD).

FilesAnywhere integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá FilesAnywhere Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett FilesAnywhere (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- A fiókok egyetlen központi helyen – az Azure felügyeleti portálján kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

FilesAnywhere az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy FilesAnywhere egyszeri bejelentkezéses engedélyezett előfizetés


> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.


Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Az éles környezetben ne használjon, ha erre szükség.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti az egy hónapos próbaidőszak [Itt](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. FilesAnywhere hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés


## <a name="adding-filesanywhere-from-the-gallery"></a>FilesAnywhere hozzáadása a katalógusból
Az Azure AD integrálása a FilesAnywhere konfigurálásához hozzá kell FilesAnywhere a katalógusból a felügyelt SaaS-alkalmazások listájára.

**FilesAnywhere hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálján](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **FilesAnywhere**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_search.png)

1. Az eredmények panelen válassza ki a **FilesAnywhere**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés FilesAnywhere a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó FilesAnywhere mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó FilesAnywhere hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** FilesAnywhere a.

Az Azure AD egyszeri bejelentkezés az FilesAnywhere tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[FilesAnywhere tesztfelhasználó létrehozása](#creating-a-filesanywhere-test-user)**  - a-megfelelője a Britta Simon szerepel, amely kapcsolódik az Azure ad-ben ábrázolása őt FilesAnywhere.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure felügyeleti portálon, és FilesAnywhere alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés FilesAnywhere, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon a a **FilesAnywhere** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanelen, **mód** kiválasztása **SAML-alapú bejelentkezés** való egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_samlbase.png)

1. Az a **FilesAnywhere tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató által kezdeményezett mód**:

    ![Egyszeri bejelentkezés konfigurálása](./media/filesanywhere-tutorial/tutorial_filesanywhere_url.png)
    
    a. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company name>.filesanywhere.com/saml20.aspx?c=215`
> [!NOTE]
> Vegye figyelembe, hogy az érték **215** van egy **clientid** , és csak egy példa. Cserélje le tényleges clientid értékét kell.

1. Az a **FilesAnywhere tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **SP által kezdeményezett mód**, hajtsa végre az alábbi lépéseket:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/filesanywhere-tutorial/tutorial_filesanywhere_url1.png)

    a. Kattintson a **speciális URL-beállítások megjelenítése** lehetőség

    b. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<sub domain>.filesanywhere.com/`

    > [!NOTE] 
    > Vegye figyelembe, hogy ezek nem állnak a valós értékeket. Az értékeket módosítsa a tényleges bejelentkezési URL-és a válasz URL-címet kell. Kapcsolattartó [FilesAnywhere támogatási csapatának](mailto:support@FilesAnywhere.com) beolvasni ezeket az értékeket. 

1. FilesAnywhere szoftveralkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Állítsa be a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/filesanywhere-tutorial/tutorial_filesanywhere_attribute.png)
    
    Amikor a FilesAnywhere regisztrál a felhasználók kapnak értékét **clientid** attribútumot [FilesAnywhere csapat](mailto:support@FilesAnywhere.com). Fel kell vennie az "Ügyfél-azonosító" attribútum FilesAnywhere által nyújtott egyedi értékkel. Az összes ezek az attribútumok fenti szükség.
    > [!NOTE] 
    > Vegye figyelembe, hogy az érték **2331** , **clientid** csak egy példa. Meg kell adnia a tényleges érték.


1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ---------------| --------------- |    
    | ClientID | *"uniquevalue"* |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson a **Ok**

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/filesanywhere-tutorial/tutorial_general_400.png)

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_certificate.png) 

1. Az a **FilesAnywhere konfigurációs** területén kattintson **konfigurálása FilesAnywhere** megnyitásához **bejelentkezés konfigurálása** ablak.

    ![Egyszeri bejelentkezés konfigurálása](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configure.png) 

    ![Egyszeri bejelentkezés konfigurálása](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configuresignon.png)

1.  Egyszeri bejelentkezési konfigurációjának teljes lekérése FilesAnywhere végén az alkalmazáshoz, lépjen kapcsolatba a [FilesAnywhere támogatási csapatának](mailto:support@FilesAnywhere.com) , és adja meg azokat a letöltött SAML jogkivonat-aláíró tanúsítvány és az egyszeri bejelentkezés (SSO) URL-CÍMÉT.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure felügyeleti portálján, Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/filesanywhere-tutorial/create_aaduser_01.png) 

1. Lépjen a **felhasználók és csoportok** kattintson **minden felhasználó** felhasználók listájának megjelenítéséhez.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/filesanywhere-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/filesanywhere-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/filesanywhere-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra. 



### <a name="creating-a-filesanywhere-test-user"></a>FilesAnywhere tesztfelhasználó létrehozása

Alkalmazás támogatja a csak az idő felhasználókiépítés, miután a felhasználók hitelesítésére, az alkalmazás automatikusan létrejön. 


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés FilesAnywhere saját hozzáférésének engedélyezésére használja.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel FilesAnywhere, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **FilesAnywhere**.

    ![Egyszeri bejelentkezés konfigurálása](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    


### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a FilesAnywhere csempére kattint, meg kell lekérése automatikusan bejelentkezett az FilesAnywhere alkalmazáshoz.


## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/FilesAnywhere-tutorial/tutorial_general_01.png
[2]: ./media/FilesAnywhere-tutorial/tutorial_general_02.png
[3]: ./media/FilesAnywhere-tutorial/tutorial_general_03.png
[4]: ./media/FilesAnywhere-tutorial/tutorial_general_04.png

[100]: ./media/FilesAnywhere-tutorial/tutorial_general_100.png

[200]: ./media/FilesAnywhere-tutorial/tutorial_general_200.png
[201]: ./media/FilesAnywhere-tutorial/tutorial_general_201.png
[202]: ./media/FilesAnywhere-tutorial/tutorial_general_202.png
[203]: ./media/FilesAnywhere-tutorial/tutorial_general_203.png
