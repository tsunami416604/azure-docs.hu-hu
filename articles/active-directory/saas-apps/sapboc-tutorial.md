---
title: 'Oktatóanyag: Azure Active Directory-integráció az SAP Business objektum Cloud |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az SAP Business objektum felhő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: ffd4480a13549caba17becff27a43f51fcaa1988
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041738"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Oktatóanyag: Azure Active Directory-integráció az SAP Business objektum Cloud

Ebben az oktatóanyagban elsajátíthatja, hogyan SAP Business objektum Felhőbeli integrálása az Azure Active Directory (Azure AD).

SAP Business objektum Felhőbeli integrálása az Azure ad-vel a következőket a következő előnyöket kínálja:

- Az Azure ad-ben szabályozhatja az SAP Business objektum Cloud férhet hozzá.
- Automatikusan bejelentkezhet a felhasználókat, hogy az SAP Business objektum Cloud egyszeri bejelentkezést és a egy felhasználó Azure AD-fiók használatával.
- A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

További információ a szoftverek az Azure ad-vel szoftverként (saas biztosított) alkalmazás integrációja, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Állítsa be az Azure AD-integráció az SAP Business objektum Cloud, a következő elemek szükségesek:

- Az Azure AD-előfizetéshez
- SAP Business objektum a egy Felhőszolgáltatás, egyszeri bejelentkezés engedélyezve

> [!NOTE]
> Ha ez az oktatóanyag lépéseit teszteli, azt javasoljuk, hogy azt ne tesztelje éles környezetben.

Ebben az oktatóanyagban a lépéseket tesztelési javaslatok:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. 

Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Az SAP Business objektum Cloud hozzáadása a katalógusból.
2. Állítsa be, és az Azure AD egyszeri bejelentkezés teszteléséhez.

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>Az SAP Business objektum Cloud hozzáadása a katalógusból
A katalógusban, az SAP Business objektum Cloud az Azure AD-integráció beállítása az SAP Business objektum Cloud kell hozzá a felügyelt SaaS-alkalmazások listájában.

Az SAP Business objektum Cloud hozzáadása a katalógusból:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali menüben válassza ki a **Azure Active Directory**. 

    ![Az Azure Active Directory gomb][1]

2. Válassza ki **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![A vállalati alkalmazások lap][2]
    
3. Új alkalmazás hozzáadásához válassza **új alkalmazás**.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **SAP Business objektum Cloud**.

    ![A keresőmezőbe](./media/sapboc-tutorial/tutorial_sapboc_search.png)

5. Az eredmények panelen válassza ki a **SAP Business objektum Cloud**, majd válassza ki **Hozzáadás**.

    ![Az SAP Business objektum Cloud a találatok listájában](./media/sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Állítsa be, és az Azure AD egyszeri bejelentkezés tesztelése

Ebben a szakaszban állítsa be, és az SAP Business objektum felhőalapú Azure AD egyszeri bejelentkezés tesztelése alapján nevű tesztfelhasználó *Britta Simon*.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, az Azure ad-ben megfelelőjére felhasználói SAP Business objektum felhőben. Azure AD-felhasználót és a kapcsolódó felhasználó SAP Business objektum felhőben közötti kapcsolat kapcsolatot kell létrehozni.

Viszony hivatkozásra, az SAP Business objektum felhőben, a **felhasználónév**, az értéket a **felhasználónév** az Azure ad-ben.

Az Azure AD egyszeri bejelentkezés az SAP Business objektum Cloud tesztelése és konfigurálása, hajtsa végre a következő feladatokat:

1. [Az Azure AD egyszeri bejelentkezés beállítása](#set-up-azure-ad-single-sign-on). Egy felhasználó beállítja a funkció használatához.
2. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user). Tesztek az Azure AD egyszeri bejelentkezés Britta Simon felhasználóval.
3. [Hozzon létre egy SAP Business objektum Cloud tesztfelhasználót](#create-an-sap-business-object-cloud-test-user). Létrehoz egy megfelelője a Britta Simon felhőben SAP Business objektum, amely a felhasználó Azure ad-ben reprezentációja van csatolva.
4. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user). Állítja be Britta Simon használata az Azure AD egyszeri bejelentkezés.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on). Ellenőrzi, hogy működik-e a konfiguráció.

### <a name="set-up-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés beállítása

Ebben a szakaszban, kapcsolja be az Azure AD egyszeri bejelentkezés az Azure Portalon. Ezután állítsa be egyszeri bejelentkezést az SAP Business objektum Cloud alkalmazásban.

Beállítása az Azure AD egyszeri bejelentkezés az SAP Business objektum felhő:

1. Az Azure Portalon az a **SAP Business objektum Cloud** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Válassza az egyszeri bejelentkezés][4]

2. Az a **egyszeri bejelentkezés** lapon a **mód**válassza **SAML-alapú bejelentkezés**.
 
    ![Válassza ki a SAML-alapú bejelentkezés](./media/sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. A **SAP Business objektum felhőalapú tartomány és URL-címek**, kövesse az alábbi lépéseket:

    1. Az a **bejelentkezési URL-** mezőbe írjon be egy URL-címet, amely rendelkezik a következő mintának: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. Az a **azonosító** mezőbe írjon be egy URL-címet, amely rendelkezik a következő mintának:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![SAP Business objektum felhőalapú tartomány és URL-címek oldal URL-címek](./media/sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > Az alábbi URL-címek értékei csak bemutatásához. Frissítse az értékeket a tényleges bejelentkezési URL-cím és az azonosító URL-t. A bejelentkezési URL-Címének lekéréséhez, lépjen kapcsolatba a [SAP Business objektum felhőalapú ügyfél-támogatási csapatának](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/en-US). Az azonosító URL-t kaphat úgy, hogy az SAP Business objektum felhőmetaadatok letölti a felügyeleti konzolon. Ennek a magyarázatát az oktatóanyag későbbi részében. 

4. A **SAML-aláíró tanúsítvány**válassza **metaadatainak XML**. Ezt követően mentse a metaadat-fájlt a számítógépen.

    ![Válassza ki a metaadatainak XML](./media/sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Kattintson a **Mentés** gombra.

    ![Mentés kiválasztása](./media/sapboc-tutorial/tutorial_general_400.png)

6. Egy másik böngészőablakban jelentkezzen be az SAP Business objektum felhőalapú vállalati hely rendszergazdaként.

7. Válassza ki **menü** > **rendszer** > **felügyeleti**.
    
    ![Válassza a menüben, majd a rendszer, majd felügyeleti](./media/sapboc-tutorial/config1.png)

8. Az a **biztonsági** lapon jelölje be a **szerkesztése** (toll) ikonra.
    
    ![A biztonság lapon válassza a Szerkesztés ikon](./media/sapboc-tutorial/config2.png)  

9. A **hitelesítési módszer**válassza **SAML egyszeri bejelentkezéses (SSO)**.

    ![SAML egyszeri bejelentkezés a hitelesítési módszer kiválasztása](./media/sapboc-tutorial/config3.png)  

10. Töltse le a service provider metaadatok (1. lépés), jelölje be **letöltése**. A metaadatok fájlban keresse meg és másolja a **entityID** értéket. Az Azure Portalon alatt **SAP Business objektum felhőalapú tartomány és URL-címek**, illessze be az értéket a **azonosító** mezőbe.

    ![Másolja és illessze be a entityID érték](./media/sapboc-tutorial/config4.png)  

11. A service provider metaadatok (2. lépés) feltölteni a fájlt a letöltött az Azure Portalról a **identitásszolgáltató feltöltése metaadatok**válassza **feltöltése**.  

    ![Identitásszolgáltató feltöltése metaadatok válassza a feltöltés](./media/sapboc-tutorial/config5.png)

12. Az a **felhasználói attribútum** listájához, válassza ki a példányhoz használni kívánt felhasználói attribútum (3. lépés). Az identitásszolgáltató rendeli hozzá a felhasználói attribútum. A felhasználó az oldalon adja meg egy egyéni attribútumot, használja a **egyéni SAML-leképezés** lehetőséget. Másik lehetőségként megadhatja **E-mail** vagy **felhasználói azonosító** felhasználói attribútumként. A példánkban a kiválasztott **E-mail** , mert azt a felhasználói azonosító jogcím-leképezve a **userprincipalname** attribútum a **felhasználói attribútumok** szakaszban az Azure-ban portál. Ez lehetővé teszi egy egyedi felhasználói e-mailt, amelyet a rendszer elküld az SAP Business objektum felhőalkalmazásba minden sikeres SAML-válasz.

    ![Válassza ki a felhasználói attribútum](./media/sapboc-tutorial/config6.png)

13. A fiók ellenőrzése az identitásszolgáltatóval (4. lépés), az a **bejelentkezési hitelesítő adatok (E-mail)** mezőbe írja be a felhasználó e-mail címét. Ezután válassza ki **fiók ellenőrzése**. A rendszer hozzáadja a felhasználói fiók bejelentkezési hitelesítő adataival.

    ![Adja meg e-mail címét, és válassza ki a fiók ellenőrzése](./media/sapboc-tutorial/config7.png)

14. Válassza ki a **mentése** ikonra.

    ![Mentés ikonra](./media/sapboc-tutorial/save.png)

> [!TIP]
> Tudjon meg ezeket az utasításokat a tömör verzióját a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazást! Válassza az alkalmazás hozzáadása után **Active Directory** > **vállalati alkalmazások**, jelölje be a **egyszeri bejelentkezés** lapon. A beágyazott dokumentáció is elérheti a **konfigurációs** szakaszban, a lap alján. További információkért lásd: [Azure ad-ben a beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ebben a szakaszban hozzon létre egy tesztfelhasználót Britta Simon nevű az Azure Portalon.

Tesztfelhasználó létrehozása az Azure ad-ben:

1. Az Azure Portalon, a bal oldali menüben válassza ki a **Azure Active Directory**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sapboc-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez válassza **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sapboc-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás**.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sapboc-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanelen töltse ki az alábbi lépéseket:
 
    1. Az a **neve** mezőbe írja be **BrittaSimon**.

    2. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    3. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    4. Kattintson a **Létrehozás** gombra.

        ![A felhasználó párbeszédpanel](./media/sapboc-tutorial/create_aaduser_04.png) 

    ![Az Azure AD-felhasználó létrehozása][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>Az SAP Business objektum Cloud tesztfelhasználó létrehozása

Az Azure AD-felhasználók ki kell építeni az SAP Business objektum felhőben megelőzően is bejelentkeznek az SAP Business objektum felhőbe. Az SAP Business objektum Cloud a kiépítés manuális feladat.

A felhasználói fiók kiépítése:

1. Jelentkezzen be rendszergazdaként az SAP Business objektum felhőalapú vállalati hely.

2. Válassza ki **menü** > **biztonsági** > **felhasználók**.

    ![Alkalmazott hozzáadása](./media/sapboc-tutorial/user1.png)

3. Az a **felhasználók** adja hozzá az új felhasználó adatai, jelölje be **+**. 

    ![Felhasználók hozzáadására szolgáló oldala](./media/sapboc-tutorial/user4.png)

    Ezután kövesse az alábbi lépéseket:

    1. Az a **Felhasználóazonosító** mezőbe írja be például a felhasználó a felhasználói azonosító **Britta**.

    2. Az a **UTÓNÉV** mezőbe írja be például a felhasználó utónevét **Britta**.

    3. Az a **Vezetéknév** mezőbe írja be például a felhasználó vezetékneve **Simon**.

    4. Az a **megjelenítendő név** mezőbe írja be például a felhasználó teljes neve **Britta Simon**.

    5. Az a **E-MAIL** mezőbe írja be például a felhasználó e-mail-címe **brittasimon@contoso.com**.

    6. Az a **szerepkörök kiválasztása** lapon válassza ki a megfelelő szerepkört a felhasználó számára, és válassza ki **OK**.

      ![Szerepkör kiválasztása](./media/sapboc-tutorial/user3.png)

    7. Válassza ki a **mentése** ikonra.    


### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban azt engedélyezi a felhasználó Britta Simon azzal a felhasználói fiók számára hozzáférést, ha az SAP Business objektum Cloud az Azure AD egyszeri bejelentkezés használatára.

Az SAP Business objektum Cloud Britta Simon hozzárendelése:

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és keresse meg a könyvtár nézetben. Válassza ki **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **SAP Business objektum Cloud**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapboc-tutorial/tutorial_sapboc_app.png) 

3. A bal oldali menüben válassza ki a **felhasználók és csoportok**.

    ![Felhasználók és csoportok kiválasztása][202] 

4. Válassza a **Hozzáadás** lehetőséget. Ezután a a **hozzárendelés hozzáadása** lapon jelölje be **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása lap][203]

5. Az a **felhasználók és csoportok** lapon, a kiválasztott felhasználók, válassza ki a **Britta Simon**.

6. Az a **felhasználók és csoportok** lapon jelölje be **kiválasztása**.

7. Az a **hozzárendelés hozzáadása** lapon jelölje be **hozzárendelése**.

![A felhasználói szerepkör hozzárendelése][200] 
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

Az SAP Business objektum Cloud csempe kiválasztásakor a hozzáférési panelen, érdemes lehet automatikusan bejelentkezett az SAP Business objektum felhőalapú alkalmazás.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/sapboc-tutorial/tutorial_general_01.png
[2]: ./media/sapboc-tutorial/tutorial_general_02.png
[3]: ./media/sapboc-tutorial/tutorial_general_03.png
[4]: ./media/sapboc-tutorial/tutorial_general_04.png

[100]: ./media/sapboc-tutorial/tutorial_general_100.png

[200]: ./media/sapboc-tutorial/tutorial_general_200.png
[201]: ./media/sapboc-tutorial/tutorial_general_201.png
[202]: ./media/sapboc-tutorial/tutorial_general_202.png
[203]: ./media/sapboc-tutorial/tutorial_general_203.png

