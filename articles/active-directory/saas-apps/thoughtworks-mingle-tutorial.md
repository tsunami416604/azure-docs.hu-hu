---
title: 'Oktatóanyag: Azure Active Directoryval integrált Thoughtworks Mingle |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Thoughtworks Mingle között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: f4729440bf523cef3f2721111852f4e5f445b5fb
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35925394"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Oktatóanyag: Azure Active Directoryval integrált Thoughtworks Mingle

Ebben az oktatóanyagban elsajátíthatja Thoughtworks Mingle integrálása Azure Active Directory (Azure AD).

Thoughtworks Mingle integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáférhet Thoughtworks Mingle Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Thoughtworks Mingle (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Thoughtworks Mingle, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Thoughtworks Mingle egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Thoughtworks Mingle hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Thoughtworks Mingle hozzáadása a gyűjteményből
Az Azure AD integrálása a Thoughtworks Mingle konfigurálásához kell hozzáadnia Thoughtworks Mingle a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Thoughtworks Mingle a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Thoughtworks Mingle**, jelölje be **Thoughtworks Mingle** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Mingle Thoughtworks](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés Thoughtworks Mingle "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Thoughtworks Mingle a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Thoughtworks Mingle közötti kapcsolat kapcsolatot kell létrehozni.

Thoughtworks Mingle, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Thoughtworks Mingle tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Thoughtworks Mingle tesztfelhasználó létrehozása](#create-a-thoughtworks-mingle-test-user)**  - való egy megfelelője a Britta Simon Thoughtworks Mingle, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Thoughtworks Mingle alkalmazásban.

**Az Azure AD az egyszeri bejelentkezés konfigurálása Thoughtworks Mingle, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Thoughtworks Mingle** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_samlbase.png)

3. Az a **Thoughtworks Mingle tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk Thoughtworks Mingle tartomány és az URL-címek](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE] 
    > Az érték nincs valós. Frissítse az értéket a tényleges bejelentkezési URL-címet. Ügyfél [Thoughtworks Mingle ügyfél-támogatási csoport](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) az értéket be kell olvasni. 
 
4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/thoughtworks-mingle-tutorial/tutorial_general_400.png)

6. Jelentkezzen be a **Thoughtworks Mingle** vállalati hely rendszergazdaként.

7. Kattintson a **Admin** lapot, és kattintson a **SSO Config**.
   
    ![Felügyelet lapját](./media/thoughtworks-mingle-tutorial/ic785157.png "SSO Config")

8. Az a **SSO Config** területen tegye a következőket:
   
    ![Egyszeri bejelentkezés Config](./media/thoughtworks-mingle-tutorial/ic785158.png "SSO Config")
    
    a. A metaadat-fájl feltöltése, kattintson a **fájl kiválasztása**. 

    b. Kattintson a **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/thoughtworks-mingle-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/thoughtworks-mingle-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![A Hozzáadás gombra.](./media/thoughtworks-mingle-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![A felhasználó párbeszédpanel](./media/thoughtworks-mingle-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-thoughtworks-mingle-test-user"></a>Thoughtworks Mingle tesztfelhasználó létrehozása

Az Azure Active Directory-felhasználók tudjanak bejelentkezni akkor ki kell építenie a Thoughtworks Mingle alkalmazást az Azure Active Directory felhasználói neveket. Thoughtworks Mingle, ha egy kézi tevékenység.

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Thoughtworks Mingle vállalati webhely.

2. Kattintson a **profil**.
   
    ![Az első projektet](./media/thoughtworks-mingle-tutorial/ic785160.png "az első projektet")

3. Kattintson a **Admin** fülre, majd **felhasználók**.
   
    ![Felhasználók](./media/thoughtworks-mingle-tutorial/ic785161.png "felhasználók")

4. Kattintson a **új felhasználó**.
   
    ![Új felhasználó](./media/thoughtworks-mingle-tutorial/ic785162.png "új felhasználó")

5. Az a **új felhasználó** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Új felhasználó párbeszédpanel](./media/thoughtworks-mingle-tutorial/ic785163.png "új felhasználó")  
 
    a. Típus a **bejelentkezési név**, **megjelenített név**, **válasszon jelszó**, **jelszó megerősítése** egy érvényes Azure AD fiók kiépítése azokat a kapcsolódó szövegmezők. 

    b. Mint **felhasználótípust**, jelölje be **teljes felhasználói**.

    c. Kattintson a **a profil létrehozása**.

>[!NOTE]
>Bármely más Thoughtworks Mingle felhasználói fiók létrehozása eszközök vagy API-k biztosítja Thoughtworks Mingle rendelkezés AAD felhasználói fiókokhoz.
> 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Thoughtworks Mingle Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Thoughtworks Mingle, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Thoughtworks Mingle**.

    ![Az alkalmazások listáját a Thoughtworks Mingle hivatkozás](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen Thoughtworks Mingle csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Thoughtworks Mingle alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/thoughtworks-mingle-tutorial/tutorial_general_01.png
[2]: ./media/thoughtworks-mingle-tutorial/tutorial_general_02.png
[3]: ./media/thoughtworks-mingle-tutorial/tutorial_general_03.png
[4]: ./media/thoughtworks-mingle-tutorial/tutorial_general_04.png

[100]: ./media/thoughtworks-mingle-tutorial/tutorial_general_100.png

[200]: ./media/thoughtworks-mingle-tutorial/tutorial_general_200.png
[201]: ./media/thoughtworks-mingle-tutorial/tutorial_general_201.png
[202]: ./media/thoughtworks-mingle-tutorial/tutorial_general_202.png
[203]: ./media/thoughtworks-mingle-tutorial/tutorial_general_203.png

