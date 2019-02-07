---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Zscaler privát hozzáférést (ZPA) |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Zscaler privát hozzáférést (ZPA) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.openlocfilehash: 8be5e94886237129824204ea166a2663e2f3110e
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814452"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Zscaler privát hozzáférést (ZPA)

Ebben az oktatóanyagban elsajátíthatja, hogyan Zscaler privát hozzáférést (ZPA) integrálása az Azure Active Directory (Azure AD).

Zscaler privát hozzáférést (ZPA) integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, aki hozzáfér a Zscaler privát hozzáférést (ZPA) Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Zscaler privát hozzáférést (ZPA) (egyszeri bejelentkezés)
- A fiókok egyetlen központi helyen – az Azure felügyeleti portálján kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Zscaler privát hozzáférést (ZPA), a következőkre van szükség:

- Azure AD-előfizetés
- A Zscaler privát hozzáférést (ZPA) egyszeri bejelentkezéses engedélyezett előfizetés


> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.


Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Az éles környezetben ne használjon, ha erre szükség.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Zscaler privát hozzáférést (ZPA) hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés


## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Zscaler privát hozzáférést (ZPA) hozzáadása a katalógusból
Konfigurálja az integrációt a Zscaler privát hozzáférést (ZPA) az Azure AD-be, szüksége Zscaler privát hozzáférést (ZPA) hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Zscaler privát hozzáférést (ZPA) hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálján](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Zscaler privát hozzáférést (ZPA)**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_001.png)

1. Az eredmények panelen válassza ki a **Zscaler privát hozzáférést (ZPA)**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Zscaler privát hozzáférést (ZPA) nevű, "Britta Simon" tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó a Zscaler privát hozzáférést (ZPA) mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Zscaler privát hozzáférést (ZPA) hivatkozás kapcsolatát kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a Zscaler privát hozzáférést (ZPA).

Az Azure AD egyszeri bejelentkezés a Zscaler privát hozzáférést (ZPA) tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **Zscaler privát hozzáférést (ZPA) tesztfelhasználó létrehozása** – szeretné, hogy egy megfelelője a Britta Simon a Zscaler privát hozzáférést (ZPA), amely kapcsolódik az Azure ad-ben ábrázolása számára.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure felügyeleti portálon, és Zscaler privát hozzáférést (ZPA) alkalmazásában egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a Zscaler privát hozzáférést (ZPA), hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon a a **Zscaler privát hozzáférést (ZPA)** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanelen, **mód** kiválasztása **SAML-alapú bejelentkezés** való egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_general_300.png)
    
1. Az a **Zscaler privát hozzáférést (ZPA) tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_01.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    b. Az a **azonosító** szövegmezőbe írja be: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE] 
    > Vegye figyelembe, hogy ezek nem állnak a valós értékeket. Hogy frissítheti ezeket az értékeket a tényleges bejelentkezési URL-és azonosító. Itt javasoljuk, hogy az azonosító URL-címe egyedi értékét használhatja. Kapcsolattartó [Zscaler privát hozzáférést (ZPA) támogatási csapatának](https://help.zscaler.com/zpa-submit-ticket) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **új tanúsítvány létrehozása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_general_400.png)     

1. Az a **új tanúsítvány létrehozása** párbeszédpanelen kattintson a naptár ikonra, és válassza ki egy **lejárati dátuma**. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_general_500.png)

1. Az a **SAML-aláíró tanúsítvány** szakaszban jelölje be **új tanúsítvány aktívvá** kattintson **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_02.png)

1. Az előugró **helyettesítő tanúsítvány** ablakban kattintson a **OK**.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_general_600.png)

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_03.png) 

1. Egy másik böngészőablakban jelentkezzen be a Zscaler privát hozzáférést (ZPA) vállalati hely rendszergazdaként.

1. Navigáljon a **rendszergazda** majd **identitásszolgáltató konfigurációja**.

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

1. Az a **identitásszolgáltató konfigurációja** területén kattintson **hozzáadása új Identitásszolgáltató konfigurációja**.

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

1. Az a **új Identitásszolgáltató konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a. Kattintson a **fájl kiválasztása** és a letöltött metaadatfájl feltöltése.

    b. Kattintson a **mentése** gombra.
    


### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure felügyeleti portálján, Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscalerprivateaccess-tutorial/create_aaduser_01.png) 

1. Lépjen a **felhasználók és csoportok** kattintson **minden felhasználó** felhasználók listájának megjelenítéséhez.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscalerprivateaccess-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscalerprivateaccess-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscalerprivateaccess-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra. 



### <a name="creating-a-zscaler-private-access-zpa-test-user"></a>Zscaler privát hozzáférést (ZPA) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon a Zscaler privát hozzáférést (ZPA) nevű felhasználó létrehozásához. Együttműködve [Zscaler privát hozzáférést (ZPA) támogatási csapatának](https://help.zscaler.com/zpa-submit-ticket) a felhasználók hozzáadása a Zscaler privát hozzáférést (ZPA) platform.


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a Zscaler privát hozzáférést (ZPA) számára a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendelje hozzá a Zscaler privát hozzáférést (ZPA), hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Zscaler privát hozzáférést (ZPA)**.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_50.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    


### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Zscaler privát hozzáférést (ZPA) csempére kattint, meg kell lekérése automatikusan bejelentkezett a Zscaler privát hozzáférést (ZPA) alkalmazás.


## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zscalerprivateaccess-tutorial/tutorial_general_01.png
[2]: ./media/zscalerprivateaccess-tutorial/tutorial_general_02.png
[3]: ./media/zscalerprivateaccess-tutorial/tutorial_general_03.png
[4]: ./media/zscalerprivateaccess-tutorial/tutorial_general_04.png

[100]: ./media/zscalerprivateaccess-tutorial/tutorial_general_100.png

[200]: ./media/zscalerprivateaccess-tutorial/tutorial_general_200.png
[201]: ./media/zscalerprivateaccess-tutorial/tutorial_general_201.png
[202]: ./media/zscalerprivateaccess-tutorial/tutorial_general_202.png
[203]: ./media/zscalerprivateaccess-tutorial/tutorial_general_203.png
