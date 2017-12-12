---
title: "Oktatóanyag: Azure Active Directory-integráció Adobe élmény-kezelővel |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az Adobe élmény Manager között."
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
ms.openlocfilehash: 4a4fccc4210fd6cf0ddbe99089c84a1fd38d5b09
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Oktatóanyag: Azure Active Directory-integráció Adobe élmény-kezelővel

Ebben az oktatóanyagban elsajátíthatja Adobe élmény Manager integrálása az Azure Active Directory (Azure AD).

Az Adobe élmény Manager integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér az Adobe élmény Manager szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Adobe élmény Manager (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Adobe élmény Managerrel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Az Adobe élmény Manager egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Adobe élmény Manager hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Adobe élmény Manager hozzáadása a gyűjteményből
Az Azure AD-be a Adobe élmény Manager-integráció konfigurálása szüksége Adobe élmény Manager hozzáadása a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**Az Adobe élmény kezelő hozzáadását a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Adobe élmény Manager**, jelölje be **Adobe élmény Manager** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Adobe élmény Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Adobe élmény Managerrel "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó Adobe élmény Manager Újdonságok egy felhasználó számára az Azure ad-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználói Adobe élmény Manager közötti kapcsolat kapcsolatot kell létrehozni.

Az Adobe élmény-kezelőben, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést az Adobe élmény Manager tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy Adobe élmény Manager tesztfelhasználó](#create-an-adobe-experience-manager-test-user)**  – Ha egy megfelelője a Britta Simon az Adobe élmény Manager, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az Adobe élmény Manager alkalmazásban az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezést az Adobe élmény Manager konfigurálásához hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Adobe élmény Manager** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. Az a **Adobe élmény Manager-tartományra és URL-címek** területen tegye a következőket, ha az alkalmazás a konfigurálni kívánt **IdP** mód:

    ![Az egyszeri bejelentkezés információk Adobe élmény Manager-tartományra és URL-címek](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. Az a **azonosító** szövegmező, írjon be egy egyedi értéket, amelyet, valamint az AEM-kiszolgálón kell megadni. 

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<AEM Server Url>/saml_login`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója és a válasz URL-CÍMEN. Ügyfél [Adobe élmény Manager támogatási munkacsoportjának](https://helpx.adobe.com/support/experience-manager.html) beolvasni ezeket az értékeket.
 
4. Ellenőrizze a megjelenítése speciális URL-cím beállításait, és hajtsa végre a következő lépést, ha konfigurálja az alkalmazást a **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Adobe élmény Manager-tartományra és URL-címek](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    Az a **URL-cím bejelentkezési** szövegmező, írja be az Adobe élmény Manager-kiszolgáló URL-címe. 

5. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. Kattintson az Adobe élmény Manager konfigurációs szakasz Adobe élmény Manager konfigurálása konfigurálása bejelentkezés ablak megnyitásához. Másolás a **SAML bejelentkezési URL-címe**, **SAML Entitásazonosító** és **Sign-Out azonosító** rövid összefoglaló szakaszából.

    ![Konfigurációs szakasz hivatkozás](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. Nyissa meg **Adobe élmény Manager** felügyeleti portál egy másik böngészőablakban.

9. Válassza ki **beállítások** -> **biztonsági** -> **felhasználók**.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Válassza ki **rendszergazda** vagy más, megfelelő felhasználó.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Válassza ki **Fiókbeállítások** -> **TrustStore létrehozása vagy kezelése**.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. Kattintson a **tanúsítványfájlt válasszon** a **tanúsítvány hozzáadása a CER-fájljával** gombra. Keresse meg és jelölje ki a tanúsítványfájlt, amely az Azure-portálról letöltött.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. A tanúsítvány a TrustStore kerül. Megjegyzés: a tanúsítványhoz tartozó aliast.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. A **felhasználók** lapon jelölje be **hitelesítési-szolgáltatás**.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Válassza ki **Fiókbeállítások** -> **létrehozása vagy kezelése KeyStore**. Hozzon létre KeyStore úgy, hogy megadja a jelszót.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Lépjen vissza a rendszergazda képernyő, és válassza ki a **beállítások** -> **műveletek** -> **Webkonzol**.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

17. Ekkor megnyílik a konfiguráció lapon.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

18. Található **Adobe Gránit SAML 2.0 hitelesítési kezelő** , majd kattintson a **Hozzáadás** ikonra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Hajtsa végre az alábbi műveleteket ezen a lapon.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Az a **elérési** szövegmezőhöz meg  **/** .

    b. A **IDP URL-cím** szövegmezőhöz értékének megadása **SAML bejelentkezési URL-címe**, amely az Azure portálról másolta.

    c. A **IDP tanúsítvány Alias** szövegmezőhöz értékének megadása **tanúsítvány Alias**, amely TrustStore meg.

    d. A **biztonsági megadott Entitásazonosító** szövegmezőhöz egyedi értékének megadása **SAML Entitásazonosító**, amely már konfigurálta az Azure portálon.

    e. A **helyességi feltétel ügyfél szolgáltatás URL-címe** szövegmezőhöz értékének megadása **válasz URL-CÍMEN**, amely már konfigurálta az Azure portálon.

    f. A **kulcs Jelszótárolójába** szövegmező, adja meg a **jelszó**, amely KeyStore értékét.

    g. A **attribútum Felhasználóazonosító** szövegmező, adja meg **Névazonosító** vagy más felhasználói Azonosítóját, amely az Ön esetében fontos.

    h. Válassza ki **automatikus létrehozás CRX felhasználók.**

    i. A **kijelentkezési URL-cím** szövegmezőhöz egyedi értékének megadása **Sign-Out URL-cím** rendelkező Azure portálról.

    j. Kattintson a **mentése**

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Az Adobe élmény Manager tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon Adobe élmény Manager nevű felhasználót hoz létre. Ha be van jelölve **automatikus létrehozás CRX felhasználók** lehetőséget, majd a felhasználók automatikusan létrejön a sikeres hitelesítés után. 

Ha azt szeretné, a felhasználók manuális létrehozásához, kérjük együttműködve [Adobe élmény Manager támogatási munkacsoportjának](https://helpx.adobe.com/support/experience-manager.html) a felhasználók hozzáadása az Adobe élmény Manager platform. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon hozzáférés biztosítása a Adobe élmény Manager által használandó Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**Az Adobe élmény Manager Britta Simon hozzárendeléséhez a következő lépésekkel:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Adobe élmény Manager**.

    ![Az alkalmazások listáját az Adobe élmény Manager hivatkozás](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel Adobe élmény Manager mozaik gombra kattint, meg kell beolvasása automatikusan bejelentkezett az Adobe élmény Manager alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_203.png

