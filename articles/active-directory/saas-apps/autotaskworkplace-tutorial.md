---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Autotask munkahelyi |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Autotask munkahelyi és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: c95310043415dc689149521716f37099bfa084c0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168940"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Autotask munkahelyi

Ebben az oktatóanyagban elsajátíthatja, hogyan Autotask munkahelyi integrálása az Azure Active Directory (Azure AD).

Autotask munkahelyi integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet Autotask munkahelyi Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Autotask munkahelyhez (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Autotask munkahelyi konfigurálni az Azure AD-integráció, a következőkre van szükség:

- Azure AD-előfizetés
- Egy Autotask munkahelyi egyszeri bejelentkezéses engedélyezett előfizetés
- Egy rendszergazda vagy a felügyelői rendszergazdai a munkaterületen kell lennie.
- Az Azure AD-ben rendszergazdai fiókkal kell rendelkeznie.
- A felhasználók ezt a szolgáltatást fogja használni, rendelkeznie kell a munkahelyi és az Azure ad-ben lévő fiókok, és az e-mail-címeket egyaránt meg kell egyeznie.

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Autotask munkahelyi hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-autotask-workplace-from-the-gallery"></a>Autotask munkahelyi hozzáadása a katalógusból
Az Azure AD integrálása a Autotask munkahelyi konfigurálásához hozzá kell Autotask munkahelyi a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Autotask munkahelyi hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Autotask munkahelyi**, jelölje be **Autotask munkahelyi** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Autotask munkahelyi](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és a egy "Britta Simon." nevű tesztelési felhasználó alapján Autotask munkahelyi az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Autotask munkahelyi mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Autotask munkahelyi hivatkozás kapcsolatát kell létrehozni.

Autotask munkaterület, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Autotask munkahelyi tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Autotask munkahelyi tesztfelhasználót](#create-an-autotask-workplace-test-user)**  – egy megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása Autotask munkaterületen.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Autotask munkahelyi alkalmazás egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Autotask munkahelyi, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Autotask munkahelyi** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_samlbase.png)

1. Ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre a következő lépéseket a **Autotask munkahelyi tartomány és URL-címek** szakaszban:

    ![Autotask munkahelyi tartomány és URL-címeket egyetlen bejelentkezési adatait az Identitásszolgáltató](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

1. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód, a jelölőnégyzet **speciális URL-beállítások megjelenítése** , és hajtsa végre az alábbi lépéseket:

    ![Autotask munkahelyi tartomány és URL-címeket egyetlen bejelentkezési adatait az SP](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.awp.autotask.net/loginsso`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [Autotask munkahelyi ügyfél-támogatási csapatának](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/autotaskworkplace-tutorial/tutorial_general_400.png)

1. Egy másik böngészőablakban jelentkezzen be a munkahelyi Online rendszergazdai hitelesítő adataival.

    >[!Note]
    >Az identitásszolgáltató konfigurálásakor altartomány kell megadni. Ellenőrizze a megfelelő együtt, jelentkezzen be munkahelyi Online. Miután bejelentkezett, jegyezze fel az URL-címét az altartomány.
    >Az altartomány része a "https://" és ".awp.autotask.net/" között, és USA, Európa, ca vagy Ausztrália kell lennie.

1. Lépjen a **konfigurációs** > **egyszeri bejelentkezés** , és hajtsa végre az alábbi lépéseket:

    ![Autotask egyszeri bejelentkezés konfigurálása](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)
 
    a. Válassza ki a **XML-metaadatfájl** lehetőséget, majd ezután töltse fel a **metaadatainak XML** az Azure-portálról letöltött.

    b. Kattintson a **egyszeri bejelentkezés engedélyezése**.
    
    ![Autotask Single Sign-on konfigurációs jóváhagyása](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Válassza ki a **megerősítem, hogy ez az információ helyességéről, valamint az identitásszolgáltató megbízható** jelölőnégyzetet.

    d. Kattintson a **jóváhagyása**.
     
>[!Note]
>Ha Autotask munkahelyi konfigurálása segítségre van szüksége, tekintse meg [ezt oldal](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) juthat a munkahelyi fiókjával.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/autotaskworkplace-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/autotaskworkplace-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/autotaskworkplace-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/autotaskworkplace-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-an-autotask-workplace-test-user"></a>Hozzon létre egy Autotask munkahelyi tesztfelhasználó számára

Ebben a szakaszban egy felhasználói Britta Simon nevű Autotask hoz létre. Együttműködve [Autotask munkahelyi támogatási csapatának](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) a felhasználók hozzáadása az Autotask munkahelyi platformon.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Autotask munkahelyi hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Autotask munkahelyi, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Autotask munkahelyi**.

    ![Az alkalmazások listáját a Autotask munkahelyi hivatkozás](./media/autotaskworkplace-tutorial/tutorial_autotaskworkplace_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Autotask munkahelyi csempére kattint, akkor kell lekérése automatikusan bejelentkezett az Autotask munkahelyi alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/autotaskworkplace-tutorial/tutorial_general_01.png
[2]: ./media/autotaskworkplace-tutorial/tutorial_general_02.png
[3]: ./media/autotaskworkplace-tutorial/tutorial_general_03.png
[4]: ./media/autotaskworkplace-tutorial/tutorial_general_04.png

[100]: ./media/autotaskworkplace-tutorial/tutorial_general_100.png

[200]: ./media/autotaskworkplace-tutorial/tutorial_general_200.png
[201]: ./media/autotaskworkplace-tutorial/tutorial_general_201.png
[202]: ./media/autotaskworkplace-tutorial/tutorial_general_202.png
[203]: ./media/autotaskworkplace-tutorial/tutorial_general_203.png

