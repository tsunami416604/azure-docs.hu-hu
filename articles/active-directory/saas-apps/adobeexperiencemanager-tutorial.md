---
title: 'Oktatóanyag: Azure Active Directory-integráció az Adobe Experience Manager |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az Adobe Experience Manager között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 56b392e57809cea0ae93800df39bb9dacd164ce2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054182"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Oktatóanyag: Azure Active Directory-integráció az Adobe Experience Manager

Ebben az oktatóanyagban elsajátíthatja, hogyan Adobe Experience Manager integrálása az Azure Active Directory (Azure AD).

Az Adobe Experience Manager integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, aki hozzáfér az Adobe Experience Manager Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első jelentkezett be az Adobe Experience Manager az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen--az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Adobe Experience Manager, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Az Adobe Experience Manager egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket tesztelése éles környezetben használata nem ajánlott.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure AD-próba környezetet [egy hónapos ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

1. Az Adobe Experience Manager hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-adobe-experience-manager-from-the-gallery"></a>Az Adobe Experience Manager hozzáadása a katalógusból
Az Azure AD-be Adobe Experience Manager-integráció konfigurálása szüksége a katalógusból az Adobe Experience Manager hozzáadása a felügyelt SaaS-alkalmazások listájában.

**Az Adobe Experience Manager hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Lépjen a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Adobe Experience Manager**. Válassza ki **Adobe Experience Manager** az eredmények panelen, és válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Adobe Experience Manager a találatok listájában](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Adobe Experience Manager egy úgynevezett "Britta Simon." tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, akik az Adobe Experience Manager tartozó felhasználó, hogy egy felhasználó Azure AD-ben. Más szóval kell létesítenie egy Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat az Adobe Experience Manager.

Az Adobe Experience Manager, az értéket adjon **felhasználónév** ugyanazt az értéket, a **felhasználónév** az Azure ad-ben. Most hozott létre a kapcsolatot a két olyan felhasználó között. 

Az Azure AD egyszeri bejelentkezés az Adobe Experience Manager tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. [Hozzon létre egy Adobe Experience Manager tesztfelhasználót](#create-an-adobe-experience-manager-test-user) szeretné, hogy egy megfelelője a Britta Simon az Adobe Experience Manager, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Adobe Experience Manager alkalmazásban az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása az Adobe Experience Manager, az alábbi lépéseket:**

1. Az Azure Portalon az a **Adobe Experience Manager** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Egyszeri bejelentkezéssel, engedélyeznie a **egyszeri bejelentkezési** párbeszédpanel a **mód** legördülő menüjében válassza **SAML-alapú bejelentkezés**.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. Az a **Adobe Experience Manager tartomány és URL-címek** területén tegye a következőket, ha az alkalmazást a konfigurálni kívánt **identitásszolgáltató** mód:

    ![Az Adobe Experience Manager tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. Az a **azonosító** mezőbe írjon be egy egyedi értéket megadhat az AEM-kiszolgálón is. 

    b. Az a **válasz URL-cím** mezőbe írja be egy URL-címet a következő mintával: `https://<AEM Server Url>/saml_login`.

    > [!NOTE] 
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóval, és a válasz URL-címe. Ezek az értékek beszerzéséhez forduljon a [Adobe Experience Manager ügyfélszolgálathoz](https://helpx.adobe.com/support/experience-manager.html).
 
4. Ellenőrizze **speciális URL-beállítások megjelenítése**. Ezután tegye a következőket, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Az Adobe Experience Manager tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    Az a **bejelentkezési URL-** mezőbe írja be az Adobe Experience Manager URL-címe. 

5. Az a **SAML-aláíró tanúsítvány** szakaszban jelölje be **tanúsítvány (Base64)**. Mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. A konfigurációs bejelentkezési ablak megnyitásához az Adobe Experience Manager konfigurációs szakaszban válassza **Adobe Experience Manager konfigurálása**. Másolás a **SAML bejelentkezési szolgáltatás URL-**, **SAML Entitásazonosító**, és **kijelentkezéses azonosító** rövid összefoglaló szakaszából.

    ![Konfigurációs szakasz hivatkozás](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés konfigurálása Mentés gombra](./media/adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. Egy másik böngésző ablakában nyissa meg a **Adobe Experience Manager** felügyeleti portálon.

9. Válassza ki **beállítások** > **biztonsági** > **felhasználók**.

    ![Az egyszeri bejelentkezés mentési gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Válassza ki **rendszergazda** vagy más, megfelelő felhasználó.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Válassza ki **Fiókbeállítások** > **kezelése TrustStore**.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. Alatt **hozzáadása tanúsítvány CER-fájlból**, kattintson a **válassza ki a tanúsítványfájl**. Keresse meg és válassza ki a tanúsítványfájlt, amely már letöltötte az Azure Portalról.

    ![Egyszeri bejelentkezés konfigurálása Mentés gombra](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. A TrustStore hozzáadta a tanúsítványt. Megjegyzés: a tanúsítvány az alias.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. Az a **felhasználók** lapon jelölje be **hitelesítési-szolgáltatás**.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Válassza ki **Fiókbeállítások** > **létrehozása/kezelése KeyStore**. Hozzon létre KeyStore jelszó megadásával.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Lépjen vissza a rendszergazdai képernyős. Válassza ki **beállítások** > **műveletek** > **Webkonzol**.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Ez megnyitja a konfiguráció lapon.

    ![Az egyszeri bejelentkezés mentési gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

17. Keresés **Adobe Gránit SAML 2.0-s hitelesítési kezelő**. Válassza ki a **Hozzáadás** ikonra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Az alábbi műveleteket ezen az oldalon.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Az a **elérési** mezőbe írja be **/**.

    b. Az a **Identitásszolgáltató URL-cím** mezőbe írja be a **SAML bejelentkezési szolgáltatás URL-** , az Azure Portalról másolt érték.

    c. Az a **Identitásszolgáltató tanúsítvány Alias** mezőbe írja be a **tanúsítvány Alias** TrustStore hozzáadott értéket.

    d. Az a **biztonsági a megadott Entitásazonosító** mezőbe írja be az egyedi **SAML Entitásazonosító** érték, amely az Azure Portalon konfigurált.

    e. Az a **helyességi feltétel fogyasztói URL-címe** mezőbe írja be a **válasz URL-cím** érték, amely az Azure Portalon konfigurált.

    f. Az a **kulcs Store jelszavát** mezőbe írja be a **jelszó** KeyStore beállított.

    g. Az a **felhasználói Attribútumazonosító** mezőbe írja be a **Névazonosító** vagy egy másik felhasználói azonosítója, amely az Ön esetében fontos.

    h. Válassza ki **automatikus létrehozás CRX felhasználók**.

    i. Az a **kijelentkezési URL-címe** mezőbe írja be az egyedi **kijelentkezéses URL-cím** érték, amely az Azure Portalról kapott.

    j. Kattintson a **Mentés** gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com) közben állítja be az alkalmazás. Ez az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** fülre. A beágyazott dokumentáció majd hozzáférni a **konfigurációs** alul található szakaszában. További tudnivalók a beágyazott dokumentáció funkció [Azure ad-ben a beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel tetején a **minden felhasználó** párbeszédpanelen jelölje ki **Hozzáadás**.

    ![A Hozzáadás gombra.](./media/adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölőnégyzetet. Jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Az Adobe Experience Manager tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon Adobe Experience Manager nevű felhasználó létrehozásához. Ha bejelölte a **automatikus létrehozás CRX felhasználók** lehetőségnél felhasználók automatikusan létrehozza a sikeres hitelesítés után. 

Ha szeretné manuálisan létrehozni a felhasználók, a [Adobe Experience Manager ügyfélszolgálathoz](https://helpx.adobe.com/support/experience-manager.html) a felhasználók hozzáadása az Adobe Experience Manager platformon. 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használandó Azure egyszeri bejelentkezés az Adobe Experience Manager hozzáférés biztosításával őket.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon Adobe Experience Manager való hozzárendeléséhez végezze el az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése. Ezután nyissa meg a könyvtár nézetben válassza **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Adobe Experience Manager**.

    ![Az alkalmazások listáját az Adobe Experience Manager hivatkozásra](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki a **Hozzáadás** gombra. Ezt követően a a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panel][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában.

6. Az a **felhasználók és csoportok** párbeszédpanelen kattintson a **kiválasztása** gombra.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza ki a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

Az Adobe Experience Manager csempe kiválasztásakor a hozzáférési panelen azt kell lekérése automatikusan bejelentkezett az Adobe Experience Manager alkalmazás.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



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

