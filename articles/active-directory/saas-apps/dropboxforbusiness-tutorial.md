---
title: 'Oktatóanyag: Azure Active Directory-integráció az Dropbox Business |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Dropbox Business és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jeedes
ms.openlocfilehash: d46f2aac5fb16b10f33cccabdcd76d60f0d6dfb9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438057"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Oktatóanyag: Azure Active Directory-integráció Dropbox Business az

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Dropbox Business az Azure Active Directory (Azure AD).

Dropbox Business integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Dropbox Business az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett, a Dropbox Business (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Dropbox Business az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Dropbox, az üzleti egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Dropbox Business hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Dropbox Business hozzáadása a katalógusból
A Dropbox, az Azure AD-be a vállalati integráció konfigurálásához, hozzá kell Dropbox Business a galériából a felügyelt SaaS-alkalmazások listájára.

**Dropbox Business hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Dropbox Business**válassza **Dropbox Business** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Dropbox Business az eredmények listájában](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Dropbox Business nevű, "Britta Simon" tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a Dropbox Business tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Dropbox Business az hivatkozás kapcsolatát kell létrehozni.

Dropbox Business, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés Dropbox Business az tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Dropbox Business tesztfelhasználó számára](#create-a-dropbox-for-business-test-user)**  – egy megfelelője a Britta Simon Dropbox Business, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a dropbox, az üzleti alkalmazás egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Dropbox Business, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Dropbox Business** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

1. Az a **Dropbox Business tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Az üzleti tartomány és URL-címek egyetlen bejelentkezési adatait a Dropboxban](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url1.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.dropbox.com/sso/<id>`

    b. Az a **azonosító** szövegmezőbe írja be egy értéket: `Dropbox`

    > [!NOTE] 
    > A fenti bejelentkezési URL-érték nem valódi értéket. A tényleges bejelentkezési URL-címet, az oktatóanyag későbbi részében ismertetett frissíteni az értéket. Kapcsolattartó [üzleti ügyfél támogatási csapata Dropbox](https://www.dropbox.com/business/contact) a gépkulcsengedélyek értékének. 
 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/dropboxforbusiness-tutorial/tutorial_general_400.png)

1. Az a **Dropbox-konfiguráció** területén kattintson **konfigurálása Dropbox Business** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Dropbox-konfiguráció](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **Dropbox Business** oldalán, a Dropbox Business bérlő ugorjon.

    a. Jelentkezzen be a Dropbox business bérlő számára. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/ic769509.png "egyszeri bejelentkezés konfigurálása")
   
    b. A bal oldali navigációs panelén kattintson **felügyeleti konzol**. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/ic769510.png "egyszeri bejelentkezés konfigurálása")
   
    c. Az a **felügyeleti konzol**, kattintson a **hitelesítési** a bal oldali navigációs ablaktáblán. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/ic769511.png "egyszeri bejelentkezés konfigurálása")
   
    d. Az a **egyszeri bejelentkezési** szakaszban jelölje be **egyszeri bejelentkezés engedélyezése**, és kattintson a **további** , ezt a szakaszt kibontva.  
   
    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/ic769512.png "egyszeri bejelentkezés konfigurálása")
   
    e. Az URL-Címének másolása a **felhasználók bejelentkezhetnek az e-mail cím beírásával vagy azok lépjen közvetlenül** és illessze be azt a **bejelentkezési URL-** szövegmezőben, hogy a **Dropbox Business tartomány és URL-címek** szakaszban az Azure Portalon. 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/ic769513.png)
    
1. Az a **egyszeri bejelentkezési** szakaszában a **hitelesítési** lapon, a következő lépésekkel: 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/dropboxforbusiness-tutorial/IC769516.png "egyszeri bejelentkezés konfigurálása")
   
    a. Kattintson a **szükséges**.
   
    b. Az a **bejelentkezési URL** szövegmezőjébe illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** az Azure Portalról másolt.

    c. Kattintson a **tanúsítvány kiválasztása**, és keresse meg a **Base64 kódolású tanúsítványfájl**.

    d. Kattintson a **módosítások mentése** a dropboxban üzleti bérlő konfigurálásának befejezéséhez.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/dropboxforbusiness-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/dropboxforbusiness-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/dropboxforbusiness-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/dropboxforbusiness-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-dropbox-for-business-test-user"></a>Hozzon létre egy Dropbox Business tesztfelhasználó számára

Ebben a szakaszban egy Britta Simon nevű felhasználó Dropbox Business jön létre. Dropbox Business támogatja a just-in-time-kiépítés, amely alapértelmezés szerint engedélyezve van.

Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik, a Dropbox Business, egy új jön létre, a Dropbox Business eléréséhez megkísérlésekor.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót, lépjen kapcsolatba kell [üzleti ügyfél támogatási csapata Dropbox](https://www.dropbox.com/business/contact) 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Dropbox Business Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Dropbox Business, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Dropbox Business**.

    ![A Dropbox, az alkalmazások listáját az üzleti hivatkozás](./media/dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a Dropbox Business csempe a hozzáférési panelen, a Dropbox bejelentkezési lapjának szerezheti be üzleti alkalmazás.
 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/dropboxforbusiness-tutorial/tutorial_general_203.png

