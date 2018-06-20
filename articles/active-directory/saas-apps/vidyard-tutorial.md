---
title: 'Oktatóanyag: Azure Active Directoryval integrált Vidyard |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Vidyard között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jeedes
ms.openlocfilehash: 0934ec2443d240234b2120056894921f2e30c81c
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213165"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Oktatóanyag: Azure Active Directoryval integrált Vidyard

Ebben az oktatóanyagban elsajátíthatja Vidyard integrálása az Azure Active Directory (Azure AD).

Vidyard integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Vidyard szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Vidyard (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Vidyard, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Vidyard egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Vidyard hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-vidyard-from-the-gallery"></a>A gyűjteményből Vidyard hozzáadása
Az Azure AD integrálása a Vidyard konfigurálásához kell hozzáadnia Vidyard a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Vidyard hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Vidyard**, jelölje be **Vidyard** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Vidyard](./media/vidyard-tutorial/tutorial_vidyard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Vidyard.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Vidyard a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Vidyard közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a Vidyard tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Vidyard tesztfelhasználó létrehozása](#create-a-vidyard-test-user)**  - való Britta Simon valami Vidyard, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Vidyard alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Vidyard, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Vidyard** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/vidyard-tutorial/tutorial_vidyard_samlbase.png)

3. Az a **Vidyard tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Vidyard tartomány és az URL-címek](./media/vidyard-tutorial/tutorial_vidyard_url2.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Vidyard tartomány és az URL-címek](./media/vidyard-tutorial/tutorial_vidyard_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Ezek az értékek nincsenek valós. A tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím, az oktatóanyag későbbi részében ismertetett később frissíteni ezeket az értékeket

1. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/vidyard-tutorial/tutorial_vidyard_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/vidyard-tutorial/tutorial_general_400.png)

7. A a **Vidyard konfigurációs** kattintson **konfigurálása Vidyard** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Vidyard konfiguráció](./media/vidyard-tutorial/tutorial_vidyard_configure.png)

8. Egy másik webes böngészőablakban jelentkezzen be a Vidyard szoftver vállalati webhely rendszergazdaként.

9. Az Vidyard irányítópulton, válassza ki a **csoport** > **biztonsági**

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure1.png)

1. Kattintson a **új profil** fülre.

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure2.png)

11. Az a **SAML-alapú konfigurációs** területen tegye a következőket:

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure3.png)

    a. Adja meg az általános profilnévnek a **profilnév** szövegmező.

    b. Másolás **egyszeri Bejelentkezéses felhasználói bejelentkezési oldal** értékét, és illessze be azt **bejelentkezési URL-cím** textbox **Vidyard tartomány és az URL-címek szakasz** Azure-portál.

    c. Másolás **ACS URL-cím** értékét, és illessze be azt **válasz URL-CÍMEN** textbox **Vidyard tartomány és az URL-címek szakasz** Azure-portál.

    d. Másolás **kibocsátó/Metadata URL-cím** értékét, és illessze be azt **azonosító** textbox **Vidyard tartomány és az URL-címek szakasz** Azure-portál.

    e. Nyissa meg a letöltött fájlt a Jegyzettömbben az Azure portálról, és illessze be azt a **X.509 tanúsítvány** szövegmező.

    f. Az a **SAML végponti URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** átmásolja az Azure-portálon.

    g. Kattintson a **megerősítése**.

12. Válassza ki az egyszeri bejelentkezés lapon **hozzárendelése** mellett egy meglévő profilt

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Miután létrehozta az egyszeri bejelentkezési profil, rendelje hozzá a csoportot, amelynek a felhasználók Azure-en keresztüli hozzáférés esetén. Ha a felhasználó nem létezik a csoport, amelyikhez hozzá volt rendelve, Vidyard automatikusan hozzon létre egy felhasználói fiókot, és a valós idejű szerepkör hozzárendelése.

13. Válassza ki a szervezet csoportot, amely látható a **hozzárendelendő rendelkezésre álló csoportok**.

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure5.png)

14. A csoportjaikon belül megjelenik a **jelenleg hozzárendelve csoportok**. Jelöljön ki egy szerepkört szerint a szervezetben, és kattintson a csoport **megerősítése**.

    ![Vidyard konfiguráció](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > További információkért tekintse meg a [a doc](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/vidyard-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/vidyard-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/vidyard-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/vidyard-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-vidyard-test-user"></a>Vidyard tesztfelhasználó létrehozása

Ez a szakasz célja Vidyard Britta Simon nevű felhasználót létrehozni. Vidyard támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve. Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az Vidyard elérésére, ha még nem létezik tett kísérlet során.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználó van szüksége, forduljon a [Vidyard támogatási csoport](mailto:support@vidyard.com).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Vidyard Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Vidyard, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Vidyard**.

    ![Az alkalmazások listáját a Vidyard hivatkozás](./media/vidyard-tutorial/tutorial_vidyard_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Vidyard csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Vidyard alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vidyard-tutorial/tutorial_general_01.png
[2]: ./media/vidyard-tutorial/tutorial_general_02.png
[3]: ./media/vidyard-tutorial/tutorial_general_03.png
[4]: ./media/vidyard-tutorial/tutorial_general_04.png

[100]: ./media/vidyard-tutorial/tutorial_general_100.png

[200]: ./media/vidyard-tutorial/tutorial_general_200.png
[201]: ./media/vidyard-tutorial/tutorial_general_201.png
[202]: ./media/vidyard-tutorial/tutorial_general_202.png
[203]: ./media/vidyard-tutorial/tutorial_general_203.png

