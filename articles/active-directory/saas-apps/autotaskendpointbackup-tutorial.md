---
title: 'Oktatóanyag: Azure Active Directory-integráció az Autotask végponti biztonsági mentési |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Autotask végponti biztonsági mentési és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9f55319e-895b-4130-8460-71713f25ed04
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeedes
ms.openlocfilehash: 7173c5d772168f6ca0c117eba56e709935146c47
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428893"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-endpoint-backup"></a>Oktatóanyag: Azure Active Directory-integráció Autotask végponti biztonsági mentéssel

Ebben az oktatóanyagban elsajátíthatja, hogyan Autotask végponti biztonsági mentési integrálható az Azure Active Directory (Azure AD).

Az Azure AD integrálása a Autotask végponti biztonsági mentést nyújt a következő előnyöket:

- Szabályozhatja, ki férhet hozzá Autotask végponti biztonsági mentést az Azure AD-ben.
- A felhasználók automatikusan első bejelentkezett Autotask végponti biztonsági mentést (egyszeri bejelentkezés), engedélyezheti az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Autotask végponti biztonsági mentést az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Autotask végponti biztonsági mentési egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Autotask végponti biztonsági mentési hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-autotask-endpoint-backup-from-the-gallery"></a>Autotask végponti biztonsági mentési hozzáadása a katalógusból
Konfigurálása az Azure AD integrálása a Autotask végponti biztonsági mentést, hozzá kell Autotask végponti biztonsági mentést a galériából a felügyelt SaaS-alkalmazások listájára.

**Autotask végponti biztonsági mentési hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Autotask végponti biztonsági mentési**, jelölje be **Autotask végponti biztonsági mentési** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Autotask végponti biztonsági mentést, a találatok listájában](./media/autotaskendpointbackup-tutorial/tutorial_autotaskendpointbackup_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Autotask végponti biztonsági mentést a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Autotask végponti biztonsági mentést a mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Autotask végponti biztonsági mentést a hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Autotask végponti biztonsági mentési tesztelése és konfigurálása, meg kell hajtsa végre a következő építőelemeit:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Autotask végponti biztonsági mentési tesztfelhasználót](#create-a-autotask-endpoint-backup-test-user)**  – egy megfelelője a Britta Simon Autotask végponti biztonsági mentést, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Autotask végponti biztonsági mentési alkalmazás egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Autotask végponti biztonsági mentést, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Autotask végponti biztonsági mentési** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/autotaskendpointbackup-tutorial/tutorial_autotaskendpointbackup_samlbase.png)

1. Az a **Autotask végponti biztonsági mentési tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Biztonsági mentés Végponttartományával Autotask és URL-címek egyszeri bejelentkezési adatait](./media/autotaskendpointbackup-tutorial/tutorial_autotaskendpointbackup_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.backup.autotask.net/singlesignon/saml/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.backup.autotask.net/singlesignon/saml/SSO`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [Autotask végponti biztonsági mentési támogatási csapatának](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/autotaskendpointbackup-tutorial/tutorial_autotaskendpointbackup_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/autotaskendpointbackup-tutorial/tutorial_general_400.png)

1. Az egyszeri bejelentkezés konfigurálása **Autotask végponti biztonsági mentési** oldalon kell küldenie a letöltött **metaadatainak XML** való [Autotask végponti biztonsági mentési támogatási csapatának](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/autotaskendpointbackup-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/autotaskendpointbackup-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/autotaskendpointbackup-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/autotaskendpointbackup-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-autotask-endpoint-backup-test-user"></a>Autotask végponti biztonsági mentési tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű Autotask végponti biztonsági mentést a felhasználó hoz létre. Együttműködve [Autotask végponti biztonsági mentési támogatási csapatának](https://backup.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) a felhasználók hozzáadása az Autotask végponti biztonsági mentési platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Autotask végponti biztonsági mentést Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel Autotask végponti biztonsági mentést, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

1. Az alkalmazások listájában jelölje ki a **Autotask végponti biztonsági mentési**.

    ![Az alkalmazások listáját a Autotask végponti biztonsági mentési hivatkozás](./media/autotaskendpointbackup-tutorial/tutorial_autotaskendpointbackup_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Autotask végponti biztonsági mentési csempére kattint, akkor kell lekérése automatikusan bejelentkezett az Autotask végponti biztonsági mentési alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/autotaskendpointbackup-tutorial/tutorial_general_01.png
[2]: ./media/autotaskendpointbackup-tutorial/tutorial_general_02.png
[3]: ./media/autotaskendpointbackup-tutorial/tutorial_general_03.png
[4]: ./media/autotaskendpointbackup-tutorial/tutorial_general_04.png

[100]: ./media/autotaskendpointbackup-tutorial/tutorial_general_100.png

[200]: ./media/autotaskendpointbackup-tutorial/tutorial_general_200.png
[201]: ./media/autotaskendpointbackup-tutorial/tutorial_general_201.png
[202]: ./media/autotaskendpointbackup-tutorial/tutorial_general_202.png
[203]: ./media/autotaskendpointbackup-tutorial/tutorial_general_203.png

