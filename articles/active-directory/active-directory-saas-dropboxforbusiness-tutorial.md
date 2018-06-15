---
title: 'Oktatóanyag: Azure Active Directoryval integrált vállalati Dropbox |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a vállalati Dropbox között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jeedes
ms.openlocfilehash: aa00a88f8325345b1b45d7d0971a03590bce1029
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342630"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Oktatóanyag: Azure Active Directoryval integrált vállalati Dropbox

Ebben az oktatóanyagban elsajátíthatja a vállalati Dropbox integrálása az Azure Active Directory (Azure AD).

A vállalati Dropbox integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Azt is szabályozhatja az Azure AD, aki hozzáférhet a vállalati a dropbox alkalmazásba.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett a dropbox alkalmazásba vállalati (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Dropbox vállalati, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Dropbox üzleti egyszeri bejelentkezést az előfizetés engedélyezve

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A vállalati Dropbox hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-dropbox-for-business-from-the-gallery"></a>A vállalati Dropbox hozzáadása a gyűjteményből
A dropbox-bA az Azure AD-be a vállalati integráció konfigurálásához kell hozzáadnia a vállalati Dropbox a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a vállalati Dropbox a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **vállalati Dropbox**, jelölje be **vállalati Dropbox** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A vállalati az eredménylistában Dropbox](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Dropbox vállalati "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a Dropbox vállalati megfelelőjére felhasználót a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a vállalati Dropbox közötti kapcsolat kapcsolatot kell létrehozni.

A Dropbox vállalati, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a vállalati Dropbox tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy üzleti tesztfelhasználó a Dropbox](#create-a-dropbox-for-business-test-user)**  - való Britta Simon egy megfelelője a Dropbox vállalati, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a dropboxban üzleti alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálása az Azure AD egyszeri bejelentkezést a vállalati Dropbox, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **vállalati Dropbox** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

3. Az a **Dropbox üzleti tartomány és az URL-címek** területen tegye a következőket:

    ![A Dropbox üzleti tartomány- és URL-címek egyetlen bejelentkezési adatok](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url1.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://www.dropbox.com/sso/<id>`

    b. Az a **azonosító** szövegmezőhöz be egy értéket: `Dropbox`

    > [!NOTE] 
    > Az előző bejelentkezési URL-cím nincs valós értékének. Az érték a tényleges bejelentkezési URL-címet, az oktatóanyag későbbi részében ismertetett frissíti. Ügyfél [Dropbox üzleti ügyfél-támogatási csoport](https://www.dropbox.com/business/contact) az értéket be kell olvasni. 
 

4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_400.png)

6. A a **üzleti konfiguráció Dropbox** kattintson **Dropbox konfigurálása a vállalati** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![A Dropbox üzleti konfiguráció](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

7. Egyszeri bejelentkezés konfigurálása **Dropbox vállalati** oldalán, nyissa meg a Dropbox üzleti bérlő számára.

    a. Jelentkezzen be a Dropbox üzleti bérlő számára. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769509.png "egyszeri bejelentkezés konfigurálása")
   
    b. A bal oldali navigációs ablaktábláján kattintson **felügyeleti konzol**. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769510.png "egyszeri bejelentkezés konfigurálása")
   
    c. Az a **felügyeleti konzol**, kattintson a **hitelesítési** a bal oldali navigációs ablaktáblán. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769511.png "egyszeri bejelentkezés konfigurálása")
   
    d. Az a **egyszeri bejelentkezés** szakaszban jelölje be **egyszeri bejelentkezés engedélyezése**, és kattintson a **további** bontsa ki az ebben a szakaszban.  
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769512.png "egyszeri bejelentkezés konfigurálása")
   
    e. A másolja az URL-címet **is jelentkeznek be az e-mail cím beírásával vagy azok lépjen közvetlenül** és illessze be azt a **bejelentkezési URL-cím** a szövegmező **üzleti tartomány és az URL-címekDropbox** Azure-portál szakaszban. 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769513.png)
    
8. Az a **egyszeri bejelentkezés** szakasza a **hitelesítési** lapon, a következő lépésekkel: 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "egyszeri bejelentkezés konfigurálása")
   
    a. Kattintson a **szükséges**.
   
    b. Az a a **bejelentkezési URL** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** , amely az Azure portálról másolta.

    c. Kattintson a **tanúsítvány kiválasztása**, majd keresse meg a **Base64 kódolású tanúsítvány fájl**.

    d. Kattintson a **módosítások mentése** a DropBox üzleti bérlő konfigurálásának befejezéséhez.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-dropbox-for-business-test-user"></a>A Dropbox az üzleti tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó a vállalati Dropbox jön létre. Vállalati Dropbox támogatja közvetlenül az időponthoz kötött kiosztást, amely alapértelmezés szerint engedélyezve van.

Nincs ebben a szakaszban az Ön művelet elem. Ha a felhasználó nem létezik a Dropbox vállalati, egy új jön létre, a vállalati Dropbox elérésére tett kísérlet során.

>[!Note]
>Ha a felhasználót manuálisan kell létrehozni, lépjen kapcsolatba kell [Dropbox üzleti ügyfél-támogatási csoport](https://www.dropbox.com/business/contact) 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés a vállalati Dropbox Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**A dropbox alkalmazásba vállalati Britta Simon hozzárendeléséhez a következő lépésekkel:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Dropbox vállalati**.

    ![A dropbox-ba, az alkalmazások listáját üzleti hivatkozás](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Amikor az üzleti csempe a hozzáférési panelen a Dropbox kattint, üzleti alkalmazás kapja meg a Dropbox bejelentkezési oldalán.
 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_203.png

