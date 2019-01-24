---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a uservoice-on |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a UserVoice között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: f8a30ba7eee7653e4a098df706450c6a4328cce9
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819067"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Oktatóanyag: Az Azure Active Directory-integráció a uservoice-on

Ebben az oktatóanyagban elsajátíthatja, hogyan UserVoice integrálása az Azure Active Directory (Azure AD).

UserVoice integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a uservoice-on Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a uservoice-hoz (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a uservoice-on, a következőkre van szükség:

- Azure AD-előfizetés
- A UserVoice egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. UserVoice hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-uservoice-from-the-gallery"></a>UserVoice hozzáadása a katalógusból
Konfigurálhatja az Azure AD integrálása a uservoice-on, hozzá kell uservoice-on a galériából a felügyelt SaaS-alkalmazások listájára.

**UserVoice hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **UserVoice**, jelölje be **UserVoice** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában a uservoice-on](./media/uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a uservoice-on a teszt "Britta Simon" nevű felhasználó.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó uservoice mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a UserVoice hivatkozás kapcsolata kell létrehozni.

Uservoice-on, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a UserVoice tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy UserVoice tesztfelhasználót](#create-a-uservoice-test-user)**  – van egy Britta Simon megfelelője a uservoice-on, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a UserVoice-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés a uservoice-on, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **UserVoice** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/uservoice-tutorial/tutorial_uservoice_samlbase.png)

1. Az a **UserVoice-tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![UserVoice-tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/uservoice-tutorial/tutorial_uservoice_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenantname>.UserVoice.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [UserVoice ügyfél-támogatási csapatának](https://www.uservoice.com/) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén másolja a **UJJLENYOMAT** tanúsítvány értékét.

    ![A tanúsítvány letöltési hivatkozás](./media/uservoice-tutorial/tutorial_uservoice_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/uservoice-tutorial/tutorial_general_400.png)

1. Az a **UserVoice konfigurációs** területén kattintson **konfigurálása UserVoice** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címet, és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![UserVoice Configuration](./media/uservoice-tutorial/tutorial_uservoice_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a UserVoice vállalati hely rendszergazdaként.

1. A felső eszköztáron kattintson **beállítások**, majd válassza ki **webportál** a menüből.
   
    ![Beállítások szakaszban az alkalmazás oldalán](./media/uservoice-tutorial/ic777519.png "beállításai")

1. Az a **webes portálon** lap a **felhasználói hitelesítés** területén kattintson **szerkesztése** megnyitásához a **felhasználói hitelesítés szerkesztése** párbeszédpanel lap.
   
    ![Webes portál lapon](./media/uservoice-tutorial/ic777520.png "webes portálon")

1. Az a **felhasználói hitelesítés szerkesztése** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Felhasználói hitelesítés szerkesztése](./media/uservoice-tutorial/ic777521.png "felhasználói hitelesítés szerkesztése")
   
    a. Kattintson a **egyszeri bejelentkezés (SSO)**.
 
    b. Illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** érték, amely az Azure Portalról történő másolta a **SSO távoli bejelentkezési** szövegmezőbe.

    c. Illessze be a **kijelentkezéses URL-cím** érték, amely az Azure Portalról történő másolta a **SSO távoli kijelentkezéses szövegmező**.
 
    d. Illessze be a **ujjlenyomat** érték, amely az Azure Portalról másolta a **jelenlegi tanúsítvány SHA1-ujjlenyomatot** szövegmezőbe.
    
    e. Kattintson a **hitelesítési beállítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/uservoice-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/uservoice-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/uservoice-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/uservoice-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-uservoice-test-user"></a>UserVoice tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a uservoice-hoz, akkor ki kell építeni UserVoice-bA. Uservoice-on, esetén kiépítése a manuális feladat.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:
1. Jelentkezzen be a **UserVoice** bérlő.

1. Lépjen a **beállítások**.
   
    ![Beállítások](./media/uservoice-tutorial/ic777811.png "beállításai")

1. Kattintson a **általános**.

1. Kattintson a **az ügynökök és az engedélyek**.
   
    ![Az ügynökök és az engedélyek](./media/uservoice-tutorial/ic777812.png "az ügynökök és engedélyek")

1. Kattintson a **rendszergazdai**.
   
    ![Adja hozzá a rendszergazdák](./media/uservoice-tutorial/ic777813.png "rendszergazdák hozzáadása")

1. Az a **rendszergazdák meghívása** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![A rendszergazdák meghívása](./media/uservoice-tutorial/ic777814.png "rendszergazdák meghívása")
   
    a. Az e-mailek szövegmezőbe írja be kíván üzembe helyezni, és kattintson a fiók e-mail-címe **Hozzáadás**.
   
    b. Kattintson a **meghívása**.

> [!NOTE]
> Bármely más UserVoice felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését UserVoice által biztosított API-k.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használandó Azure egyszeri bejelentkezés a uservoice-hoz a hozzáférés biztosításával.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel a uservoice-on, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **UserVoice**.

    ![A UserVoice-hivatkozás alkalmazásainak listájában](./media/uservoice-tutorial/tutorial_uservoice_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a UserVoice csempére kattint, meg kell beolvasása automatikusan bejelentkezett a UserVoice-alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/uservoice-tutorial/tutorial_general_01.png
[2]: ./media/uservoice-tutorial/tutorial_general_02.png
[3]: ./media/uservoice-tutorial/tutorial_general_03.png
[4]: ./media/uservoice-tutorial/tutorial_general_04.png

[100]: ./media/uservoice-tutorial/tutorial_general_100.png

[200]: ./media/uservoice-tutorial/tutorial_general_200.png
[201]: ./media/uservoice-tutorial/tutorial_general_201.png
[202]: ./media/uservoice-tutorial/tutorial_general_202.png
[203]: ./media/uservoice-tutorial/tutorial_general_203.png

