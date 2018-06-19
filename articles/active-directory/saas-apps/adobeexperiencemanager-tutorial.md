---
title: 'Oktatóanyag: Azure Active Directory-integráció Adobe élmény-kezelővel |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az Adobe élmény Manager között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 4904d9b0fc0746bc915477bb0ac83a0083ca198f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35926184"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Oktatóanyag: Azure Active Directory-integráció Adobe élmény-kezelővel

Ebben az oktatóanyagban elsajátíthatja Adobe élmény Manager integrálása az Azure Active Directory (Azure AD).

Az Adobe élmény Manager integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér az Adobe élmény Manager szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása aláírni Adobe élmény Manager az Azure AD-fiókok.
- A fiók egyetlen központi helyen--az Azure-portálon kezelheti.

Az Azure AD SaaS integrálásáról további információért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Adobe élmény Managerrel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Az Adobe élmény Manager egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Nem ajánlott, éles környezetben teszteléséhez lépéseit az oktatóanyag segítségével.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben [ingyenes egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Az ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

1. Adobe élmény Manager hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-adobe-experience-manager-from-the-gallery"></a>A gyűjteményből Adobe élmény kezelő hozzáadása
Az Azure AD-be a Adobe élmény Manager-integráció konfigurálása szüksége Adobe élmény Manager hozzáadása a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**A gyűjteményből Adobe élmény Manager hozzáadásához tegye a következőket:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Ugrás a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Egy új alkalmazást szeretne telepíteni, válassza ki a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Adobe élmény Manager**. Válassza ki **Adobe élmény Manager** az eredmények panelen, és válassza ki azt a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Adobe élmény Manager](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Adobe élmény Managerrel "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, aki a párjukhoz felhasználó Adobe élmény Manager egy felhasználó számára az Azure AD. Más szóval kell Adobe élmény Manager egy Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat létrehozásához.

Az Adobe élmény-kezelőben adjon a érték **felhasználónév** ugyanazt az értéket, a **felhasználónév** az Azure ad-ben. Most hozott létre a hivatkozást a két felhasználó között. 

Az Azure AD egyszeri bejelentkezést az Adobe élmény Manager tesztelése és konfigurálása, végezze el a következő építőelemeket:

1. [Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy a felhasználók számára a szolgáltatás használatához.
2. [Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. [Hozzon létre egy Adobe élmény Manager tesztfelhasználó](#create-an-adobe-experience-manager-test-user) szeretné, hogy egy Britta Simon megfelelője az Adobe élmény Manager, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. [Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az Adobe élmény Manager alkalmazásban az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezést az Adobe élmény Manager konfigurálásához tegye a következőket:**

1. Az Azure portálon a a **Adobe élmény Manager** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Is engedélyezhető az egyszeri bejelentkezés, a **egyszeri bejelentkezés** párbeszédpanel a **mód** legördülő menüben válassza **SAML-alapú bejelentkezés**.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. Az a **Adobe élmény Manager-tartományra és URL-címek** területen tegye a következőket, ha az alkalmazás a konfigurálni kívánt **IdP** mód:

    ![Az egyszeri bejelentkezés információk Adobe élmény Manager-tartományra és URL-címek](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. Az a **azonosító** mezőbe írjon be egy egyedi értéket az AEM-kiszolgálón is megadhat. 

    b. Az a **válasz URL-CÍMEN** mezőbe írja be olyan URL-címe a következő mintát: `https://<AEM Server Url>/saml_login`.

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. A tényleges azonosítójú frissítheti ezeket az értékeket, és válasz URL-CÍMÉT. Ahhoz, hogy ezeket az értékeket, lépjen kapcsolatba a [Adobe élmény Manager támogatási munkacsoportjának](https://helpx.adobe.com/support/experience-manager.html).
 
4. Ellenőrizze **megjelenítése speciális URL-beállításainak**. Majd hajtsa végre az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Adobe élmény Manager-tartományra és URL-címek](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    Az a **URL-cím bejelentkezési** mezőben adja meg az Adobe élmény Manager-kiszolgáló URL-címe. 

5. Az a **SAML-aláíró tanúsítványa** szakaszban jelölje be **tanúsítvány (Base64)**. Mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. Az Adobe élmény Manager konfigurációs szakaszban a konfigurációs bejelentkezés ablak megnyitásához válassza **Adobe élmény Manager konfigurálása**. Másolás a **SAML bejelentkezési URL-címe**, **SAML Entitásazonosító**, és **Sign-Out azonosító** rövid összefoglaló szakaszából.

    ![Konfigurációs szakasz hivatkozás](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés mentési gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. Egy másik böngészőablakban nyissa meg a **Adobe élmény Manager** felügyeleti portálon.

9. Válassza ki **beállítások** > **biztonsági** > **felhasználók**.

    ![Az egyszeri bejelentkezés mentési gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Válassza ki **rendszergazda** vagy más, megfelelő felhasználó.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Válassza ki **Fiókbeállítások** > **kezelése TrustStore**.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. A **tanúsítvány hozzáadása a CER-fájljával**, kattintson a **tanúsítványfájlt válasszon**. Keresse meg és jelölje ki a tanúsítványfájlt, amely már letöltötte az Azure portálról.

    ![Egyszeri bejelentkezés mentési gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. A tanúsítvány a TrustStore kerül. Megjegyzés: a tanúsítványhoz tartozó aliast.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. Az a **felhasználók** lapon jelölje be **hitelesítési-szolgáltatás**.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Válassza ki **Fiókbeállítások** > **létrehozása vagy kezelése KeyStore**. Hozzon létre KeyStore úgy, hogy megadja a jelszót.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Lépjen vissza, ha a rendszergazda a képernyőre. Válassza ki **beállítások** > **műveletek** > **Webkonzol**.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Ekkor megnyílik a konfiguráció lapon.

    ![Az egyszeri bejelentkezés mentési gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

17. Található **Adobe Gránit SAML 2.0 hitelesítési kezelő**. Válassza ki a **Hozzáadás** ikonra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Hajtsa végre a következő műveleteket ezen a lapon.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Az a **elérési** adja meg a **/**.

    b. Az a **IDP URL-cím** mezőbe írja be a **SAML bejelentkezési URL-címe** Azure-portálról másolt érték.

    c. Az a **IDP tanúsítvány Alias** mezőbe írja be a **tanúsítvány Alias** TrustStore hozzáadott értéket.

    d. Az a **biztonsági megadott Entitásazonosító** mezőben adja meg az egyedi **SAML Entitásazonosító** érték, amely konfigurálta az Azure portálon.

    e. Az a **helyességi feltétel ügyfél szolgáltatás URL-címe** mezőbe írja be a **válasz URL-CÍMEN** érték, amely konfigurálta az Azure portálon.

    f. Az a **kulcs Jelszótárolójába** mezőbe írja be a **jelszó** KeyStore beállított.

    g. Az a **attribútum Felhasználóazonosító** mezőbe írja be a **Névazonosító** vagy egy másik felhasználói Azonosítóját, abban az esetben, ha szükséges.

    h. Válassza ki **automatikus létrehozás CRX felhasználók**.

    i. Az a **kijelentkezési URL-cím** mezőben adja meg az egyedi **Sign-Out URL-cím** érték, amely az Azure-portálon származik.

    j. Kattintson a **Mentés** gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com) közben állítja be az alkalmazást. Ez az alkalmazás a hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** fülre. Majd hozzáférni a beágyazott dokumentáció keresztül a **konfigurációs** szakasz alján. További, a beágyazott dokumentáció szolgáltatásról [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure ad-ben, a következő lépéseket:**

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Lehetőségre a **felhasználói** párbeszédpanel tetején a **minden felhasználó** párbeszédpanelen jelölje ki **Hozzáadás**.

    ![A Hozzáadás gombra.](./media/adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanel mezőben a következő lépéseket:

    ![A felhasználó párbeszédpanel](./media/adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölőnégyzetet. Jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Az Adobe élmény Manager tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon Adobe élmény Manager nevű felhasználót hoz létre. Ha bejelölte a **automatikus létrehozás CRX felhasználók** beállítás, felhasználók jönnek létre automatikusan a sikeres hitelesítés után. 

Ha azt szeretné, a felhasználók manuális létrehozásához, a [Adobe élmény Manager támogatási munkacsoportjának](https://helpx.adobe.com/support/experience-manager.html) a felhasználók hozzáadása az Adobe élmény Manager platform. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon nyújtó őket az Adobe élmény Manager Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Az Adobe élmény Manager Britta Simon hozzárendeléséhez a következő lépéseket:**

1. Nyissa meg az alkalmazások megtekintése az Azure-portálon. A könyvtár nézetben jelölje ki a következő Ugrás **vállalati alkalmazások**, majd válassza ki **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Adobe élmény Manager**.

    ![Az alkalmazások listáját az Adobe élmény Manager hivatkozás](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. A bal oldali menüben válasszon ki **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki a **Hozzáadás** gombra. Ezt követően a a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában.

6. Az a **felhasználók és csoportok** párbeszédpanel, kattintson a **válasszon** gombra.

7. Az a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

Az Adobe élmény kezelője csempe a hozzáférési panelen válassza ki, amikor meg kell beolvasni automatikusan megtörténik a az Adobe élmény Manager alkalmazáshoz.

A hozzáférési panel kapcsolatos további információkért lásd: [a hozzáférési panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/adobeexperiencemanager-tutorial/tutorial_general_203.png

