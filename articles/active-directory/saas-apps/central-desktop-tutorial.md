---
title: 'Oktatóanyag: Azure Active Directoryval integrált központi asztali |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a központi asztali között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 192576a27475664422814ce68c2d5500d1e39ba9
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35927599"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Oktatóanyag: Azure Active Directory-integráció központi asztal

Ebben az oktatóanyagban elsajátíthatja központi asztali integrálása az Azure Active Directory (Azure AD).

Központi asztali integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér központi asztali szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírva központi asztalhoz az Azure AD-fiókok.
- A fiók egyetlen központi helyen--az Azure-portálon kezelheti.

Az Azure AD SaaS alkalmazásintegráció kapcsolatos további tudnivalókért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a központi asztal, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egyetlen központi asztali bejelentkezési a alkalmas előfizetés

> [!NOTE]
> Nem ajánlott, éles környezetben teszteléséhez lépéseit az oktatóanyag segítségével.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem telepítette az Azure AD próbaverziójának környezetben [ingyenes egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Az ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

1. Központi asztali hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-central-desktop-from-the-gallery"></a>Adja hozzá a központi asztali a gyűjteményből
Az Azure AD integrálása a központi asztali konfigurálásához kell hozzáadnia központi asztali a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből központi asztali hozzáadásához tegye a következőket:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Ugrás a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazások hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **központi asztali**. Válassza ki **központi asztali** az eredmények panelen, és válassza ki azt a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában központi asztali](./media/central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a központi Desktop "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, aki a párjukhoz felhasználó központi asztali egy felhasználó számára az Azure AD. Más szóval kell a központi asztali egy Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat létrehozásához.

A központi asztali, adjon **felhasználónév** azonos érték, mint a **felhasználónév** az Azure ad-ben. Most hozott létre a hivatkozást a két felhasználó között.

Az Azure AD egyszeri bejelentkezést a központi Desktop tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. [Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy a felhasználók számára a szolgáltatás használatához.
2. [Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. [Hozzon létre egy központi asztali tesztfelhasználó](#create-a-central-desktop-test-user) való egy megfelelője a Britta Simon központi asztali, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. [Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzése.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a központi asztali alkalmazások konfigurálása egyszeri bejelentkezéshez.

**A központi asztal konfigurálása az Azure AD egyszeri bejelentkezést, tegye a következőket:**

1. Az Azure portálon a a **központi asztali** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Is engedélyezhető az egyszeri bejelentkezés, a **egyszeri bejelentkezés** párbeszédpanel a **mód** legördülő listában válassza **SAML-alapú bejelentkezés**.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

3. Az a **központi asztali tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk központi asztali tartomány és az URL-címek](./media/central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. Az a **bejelentkezési URL-cím** mezőbe írja be a következő mintát olyan URL-címe: `https://<companyname>.centraldesktop.com`

    b. Az a **azonosító** mezőbe írja be a következő mintát olyan URL-címe:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. Az a **válasz URL-CÍMEN** mezőbe írja be a következő mintát olyan URL-címe: `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítójú válasz URL-címet, és a bejelentkezési URL-cím. Lépjen kapcsolatba a [központi asztali ügyfél-támogatási csoport](https://imeetcentral.com/contact-us) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** szakaszban jelölje be **tanúsítvány**. Mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

5. Válassza ki a **Mentés** gombot.

    ![Az egyszeri bejelentkezés mentési gomb konfigurálása](./media/central-desktop-tutorial/tutorial_general_400.png)
    
6. Az a **központi asztali konfigurálása** szakaszban jelölje be **központi asztal konfigurálása** megnyitásához a **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló** szakasz.

    ![Központi asztali konfigurálása](./media/central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

7. Jelentkezzen be a **központi asztali** bérlő.

8. Ugrás a **beállítások**. Válassza ki **speciális**, majd válassza ki **egyszeri bejelentkezés**.

    ![A telepítő - speciális](./media/central-desktop-tutorial/ic769563.png "telepítő – speciális")

9. Az a **egyetlen bejelentkezést a beállítások** lapján tegye a következőket:

    ![Az egyszeri bejelentkezés beállítások](./media/central-desktop-tutorial/ic769564.png "egyetlen bejelentkezést a beállítások")
    
    a. Válassza ki **engedélyezése SAML v2 az egyszeri bejelentkezés**.
    
    b. Az a **egyszeri bejelentkezési URL-cím** mezőbe illessze be a **SAML Entitásazonosító** Azure-portálról másolt érték.
    
    c. Az a **Egyszeri bejelentkezési URL-cím** mezőbe illessze be a **SAML-alapú egyszeri bejelentkezési URL-címe** Azure-portálról másolt érték.
    
    d. Az a **SSO kijelentkezési URL-cím** mezőbe illessze be a **Sign-Out URL-cím** Azure-portálról másolt érték.

10. Az a **üzenet aláírás-ellenőrzési módszer** területen tegye a következőket:

    ![Üzenet-aláírást ellenőrzési módszer](./media/central-desktop-tutorial/ic769565.png "üzenet aláírás-ellenőrzési módszer") egy. Válassza a **Tanúsítvány** elemet.
    
    b. Az a **SSO tanúsítvány** listáról válassza ki **RSH SHA256**.
    
    c. Nyissa meg a letöltött tanúsítvány a Jegyzettömbben. Ezután másolja a tartalmat a tanúsítványt, és illessze be azt a **SSO tanúsítvány** mező.
        
    d. Válassza ki **jeleníti meg a hivatkozást a SAMLv2 bejelentkezési**.
    
    e. Válassza ki **frissítés**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com) közben állítja be az alkalmazást. Ez az alkalmazás a hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapot, és hozzáférhet a beágyazott a dokumentáció a **konfigurációs** alsó szakasz. További, a beágyazott dokumentáció szolgáltatásról [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure ad-ben, a következő lépéseket:**

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/central-desktop-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**. Válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/central-desktop-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/central-desktop-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanel mezőben a következő lépéseket:

    ![A felhasználó párbeszédpanel](./media/central-desktop-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-central-desktop-test-user"></a>Központi asztali tesztfelhasználó létrehozása

Az Azure Active Directory-felhasználók tudjanak bejelentkezni akkor ki kell építenie a központi asztali alkalmazás. Ez a szakasz ismerteti az Azure AD-felhasználói fiókok létrehozása a központi asztali.

> [!NOTE]
> Azure AD-felhasználói fiókok létrehozásához használhatja a központi asztali felhasználói fiók létrehozása eszközei vagy központi asztali által nyújtott API-k.

**Felhasználói fiókok központi asztalra telepítéséhez:**

1. Jelentkezzen be a központi asztali bérlő.

2. Ugrás a **személyek** > **belső tagok**.

3. Válassza ki **belső tagok felvétele**.

    ![Személyek](./media/central-desktop-tutorial/ic781051.png "személyek")
    
4. Az a **E-mail címet az új tagok** mezőbe írja be az Azure AD-fiókot, amelyet szeretne kiépíteni, majd válassza ki **következő**.

    ![E-mail-címeket az új tagok](./media/central-desktop-tutorial/ic781052.png "E-mail címet az új tagok")

5. Válassza ki **hozzáadása belső tag**.

    ![Hozzáadás belső tag](./media/central-desktop-tutorial/ic781053.png "belső tag hozzáadása")
   
   >[!NOTE]
   >A felhasználók a fiókok aktiválásához megerősítési hivatkozást tartalmazó e-mailt kapni.
   
### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban felhasználói Britta Simon központi asztali hozzáférést adna által használandó Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**Központi asztali Britta Simon hozzárendeléséhez a következő lépéseket:**

1. Nyissa meg az alkalmazások megtekintése az Azure-portálon. Nyissa meg a könyvtár nézetet, és folytassa a **vállalati alkalmazások**.

2. Válassza ki **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **központi asztali**.

    ![Az alkalmazások listáját a központi asztali hivatkozás](./media/central-desktop-tutorial/tutorial_centraldesktop_app.png)  

3. A bal oldali menüben válasszon ki **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a a **hozzáadása hozzárendelés** párbeszédpanel megnyitásához.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a a **felhasználók** listája.

6. Az a **felhasználók és csoportok** párbeszédpanel, kattintson a **válasszon** gombra.

7. Az a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel használatával.

Ha a központi asztali csempe a hozzáférési panelen válassza ki, automatikusan beolvasása jelentkezett be a központi asztali alkalmazások.
A hozzáférési panel kapcsolatos további információkért lásd: [a hozzáférési panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/central-desktop-tutorial/tutorial_general_203.png

