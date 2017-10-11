---
title: "Oktatóanyag: Azure Active Directory-integráció SAP üzleti objektum a felhő |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az SAP Business objektumot felhő között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 6d517c5e302ac36e5bba2053998c75f8f4d42683
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Oktatóanyag: Azure Active Directory-integráció SAP üzleti objektum a felhő

Ebben az oktatóanyagban elsajátíthatja SAP Business objektumot felhő integrálása az Azure Active Directory (Azure AD).

SAP Business objektumot felhőalapú Azure AD-val integrálásakor kapott a következő előnyöket biztosítja:

- Az Azure ad-ben szabályozhatja, ki férhet hozzá az SAP Business objektumot felhő.
- Automatikusan bejelentkezhet a felhasználókat, hogy SAP Business objektumot felhő egyszeri bejelentkezést és a felhasználó Azure AD-fiókot.
- A fiók egyetlen, központi helyen, az Azure-portálon kezelheti.

További információért, egy szolgáltatott szoftverként (SaaS) alkalmazás integráció az Azure ad-vel kapcsolatban lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

A SAP Business objektumot felhőalapú Azure AD-integráció beállítása, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy SAP üzleti objektum felhőben, az egyszeri bejelentkezés engedélyezve

> [!NOTE]
> Ha ebben az oktatóanyagban teszteli a lépéseket, azt javasoljuk, hogy ne tesztelje azokat éles környezetben.

Ebben az oktatóanyagban a lépéseket tesztelési ajánlásokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [ingyenes egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. 

Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. SAP Business objektumot felhő hozzáadása a gyűjteményből.
2. Állítsa be, és az Azure AD az egyszeri bejelentkezés tesztelése.

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>SAP Business objektumot felhő hozzáadása a gyűjteményből
A SAP üzleti objektum felhőalapú Azure ad-integráció beállítása a katalógusban, vegye fel SAP Business objektumot felhőalapú a kezelt SaaS-alkalmazások listáját.

SAP Business objektumot felhő hozzáadása a gyűjteményből:

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali menüben válassza ki a **Azure Active Directory**. 

    ![Az Azure Active Directory gomb][1]

2. Válassza ki **vállalati alkalmazások**, majd válassza ki **összes alkalmazás**.

    ![A vállalati alkalmazások lap][2]
    
3. Új alkalmazás hozzáadásához válassza **új alkalmazás**.

    ![Az új alkalmazás gomb][3]

4. A keresési mezőbe, írja be a **SAP Business objektumot felhő**.

    ![A keresési mezőbe](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. Az eredmények panelen válassza ki a **SAP Business objektumot felhő**, majd válassza ki **Hozzáadás**.

    ![SAP Business objektumot felhő az eredménylistában](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Állítsa be, és az Azure AD az egyszeri bejelentkezés tesztelése

Ebben a szakaszban a beállítása és tesztelése az Azure AD az egyszeri bejelentkezés SAP üzleti objektum a felhő alapú nevű tesztfelhasználó *Britta Simon*.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, az Azure AD-partner felhasználó SAP Business objektumot felhőben. Egy Azure AD-felhasználó és a kapcsolódó felhasználó SAP Business objektumot felhőben közötti kapcsolat kapcsolatot kell létrehozni.

A hivatkozás kapcsolat, SAP Business objektumot felhő létrehozására a **felhasználónév**, rendelje az értékét a **felhasználónév** az Azure ad-ben.

Az Azure AD egyszeri bejelentkezést a SAP Business objektumot felhőalapú tesztelése és konfigurálása, végezze el a következő feladatokat:

1. [Az Azure AD az egyszeri bejelentkezés beállítása](#set-up-azure-ad-single-sign-on). A felhasználó beállítja a szolgáltatás használatához.
2. [Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user). Az Azure AD tesztek egyszeri bejelentkezés Britta Simon felhasználóval.
3. [Hozzon létre egy SAP Business objektumot felhő tesztfelhasználó](#create-an-sap-business-object-cloud-test-user). Létrehoz egy Britta Simon megfelelője a felhőben SAP Business objektumot, amely csatolva van a felhasználó az Azure AD ábrázolását.
4. [Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user). Állítja be az Azure AD használatára Britta Simon egyszeri bejelentkezés.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on). Ellenőrzi, hogy működik-e a konfiguráció.

### <a name="set-up-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés beállítása

Ebben a szakaszban bekapcsolása az Azure AD-egyszeri bejelentkezés az Azure portálon. Ezután állítsa be az egyszeri bejelentkezés az SAP Business objektumot felhőalapú alkalmazásokhoz.

Beállítása az Azure AD egyszeri bejelentkezést a SAP Business objektumot felhőalapú:

1. Az Azure portálon a a **SAP Business objektumot felhő** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Válassza ki az egyszeri bejelentkezés][4]

2. Az a **egyszeri bejelentkezés** lap, a **mód**, jelölje be **SAML-alapú bejelentkezés**.
 
    ![Válassza ki a SAML-alapú bejelentkezés](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. A **SAP Business objektumot felhőalapú tartományt és URL-címek**, kövesse az alábbi lépéseket:

    1. Az a **bejelentkezési URL-cím** mezőbe írjon be egy URL-címet, amely rendelkezik a következő mintát: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. Az a **azonosító** mezőbe írjon be egy URL-címet, amely rendelkezik a következő mintát:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![SAP Business objektumot felhőalapú tartományt és URL-címek lap URL-címek](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > Az URL-címek értékei csak bemutatásához. Módosítsa a tényleges bejelentkezési URL-cím és azonosító URL-t. A bejelentkezési URL-cím beszerzése, lépjen kapcsolatba a [SAP Business objektumot felhőalapú ügyfél-támogatási csoport](https://www.sap.com/product/analytics/cloud-analytics.support.html). Az azonosító URL-t kaphat az SAP Business objektumot felhő metaadatok letöltése a felügyeleti konzolon. Ennek a magyarázatát az oktatóanyag későbbi részében. 

4. A **SAML-aláíró tanúsítványa**, jelölje be **metaadatainak XML-kódja**. Mentse a metaadat-fájlt a számítógépen.

    ![Válassza ki a metaadatok XML](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Kattintson a **Mentés** gombra.

    ![A mentés kiválasztása](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. Egy másik webes böngészőablakban jelentkezzen be a SAP Business objektumot felhő vállalati hely rendszergazdaként.

7. Válassza ki **menü** > **rendszer** > **felügyeleti**.
    
    ![Válassza ki a menüben, majd a rendszer, majd felügyeleti](./media/active-directory-saas-sapboc-tutorial/config1.png)

8. Az a **biztonsági** lapon jelölje be a **szerkesztése** (toll) ikonra.
    
    ![A biztonság lapon válassza ki a Szerkesztés ikonra](./media/active-directory-saas-sapboc-tutorial/config2.png)  

9. A **hitelesítési módszer**, jelölje be **SAML-alapú egyszeri bejelentkezés (SSO)**.

    ![SAML-alapú egyszeri bejelentkezést a hitelesítési mód kiválasztása](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. Töltse le a service provider metaadatok (1. lépés), jelölje be **letöltése**. A metaadatok fájlban keresse meg és másolja a **entityid beállítást** érték. Az Azure portálon a **SAP Business objektumot felhőalapú tartományt és URL-címek**, illessze be az értéket a **azonosító** mezőbe.

    ![Másolja és illessze be a entityid beállítást érték](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. A service provider metaadatok (2. lépés) feltölteni a fájlt a letöltött Azure-portálról, a **identitásszolgáltató feltöltése metaadatok**, jelölje be **feltöltése**.  

    ![A metaadatok feltöltése identitásszolgáltató, területen válassza a feltöltés](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. Az a **felhasználói attribútum** listára, válassza ki a megvalósítás a használni kívánt felhasználói attribútum (3. lépés). A felhasználói attribútum az identitásszolgáltató van leképezve. Egyéni attribútumot a felhasználói oldal, használja a **egyéni SAML leképezési** lehetőséget. Vagy, akkor ki lehet **E-mail** vagy **Felhasználóazonosító** felhasználói attribútumként. Ebben a példában, hogy kiválasztott **E-mail** mivel azt a felhasználói azonosító jogcímet leképezése a **userprincipalname** attribútumnak a **felhasználói attribútumok** szakaszban az Azure-ban portál. Ez lehetővé teszi egy egyedi felhasználói e-mailt, az SAP Business objektumot felhő alkalmazás minden sikeres SAML válaszként küldött.

    ![Válassza ki a felhasználói attribútum](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. Ellenőrizzük a fiókját az identitásszolgáltató (4. lépés), az a a **bejelentkezési hitelesítő adatok (E-mail)** mezőbe írja be a felhasználó e-mail címét. Ezt követően válassza **ellenőrizze fiókja**. A rendszer hozzáadja a felhasználói fiók bejelentkezési hitelesítő adataival.

    ![Adja meg e-mail címét, és válassza ki azt a fiókot ellenőrzése](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. Válassza ki a **mentése** ikonra.

    ![Mentés ikonra](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> Ezek az utasítások a tömör verzióját el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg a állítja be az alkalmazás! Miután hozzáadta az alkalmazás kiválasztásával **Active Directory** > **vállalati alkalmazások**, jelölje be a **egyszeri bejelentkezés** lapon. A beágyazott dokumentációja a **konfigurációs** szakaszban, a lap alján. További információkért lásd: [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ebben a szakaszban az Azure portálon Britta Simon nevű tesztfelhasználó létrehozása.

Tesztfelhasználó létrehozása az Azure ad-ben:

1. Az Azure portálon a bal oldali menüben válassza ki a **Azure Active Directory**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. Válassza ki azon felhasználók listájának megjelenítéséhez **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás**.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanelen töltse ki az alábbi lépéseket:
 
    1. Az a **neve** adja meg a **BrittaSimon**.

    2. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail címét.

    3. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    4. Kattintson a **Létrehozás** gombra.

        ![A felhasználó párbeszédpanel](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    ![Az Azure AD-felhasználó létrehozása][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>Az SAP Business objektumot felhő tesztfelhasználó létrehozása

Az Azure Active Directory-felhasználók ki kell építenie SAP Business objektumot felhőben, mielőtt bejelentkeznének SAP Business objektumot felhőbe. SAP Business objektumot felhőben kézi tevékenység.

A felhasználói fiók létrehozásához:

1. Jelentkezzen be rendszergazdaként a SAP Business objektumot felhő vállalati webhelyre.

2. Válassza ki **menü** > **biztonsági** > **felhasználók**.

    ![Alkalmazott hozzáadása](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. Az a **felhasználók** lapra, adja hozzá az új felhasználó adatait, válassza ki  **+** . 

    ![Felhasználók hozzáadására szolgáló oldala](./media/active-directory-saas-sapboc-tutorial/user4.png)

    Ezután kövesse az alábbi lépéseket:

    1. Az a **Felhasználóazonosító** mezőbe írja be például a felhasználó a felhasználói azonosító **Britta**.

    2. Az a **UTÓNÉV** mezőbe írja be például a felhasználó utóneve **Britta**.

    3. Az a **Vezetéknév** mezőbe írja be például a felhasználó vezetékneve **Simon**.

    4. Az a **MEGJELENÍTETT név** mezőbe írja be például a teljes nevet, a felhasználó **Britta Simon**.

    5. Az a **E-MAIL** mezőbe írja be például a felhasználó e-mail címe  **brittasimon@contoso.com** .

    6. Az a **szerepkörök kiválasztása** lapon válassza ki a megfelelő szerepkört a felhasználó számára, és válassza ki **OK**.

      ![Szerepkör kiválasztása](./media/active-directory-saas-sapboc-tutorial/user3.png)

    7. Válassza ki a **mentése** ikonra.    


### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze a felhasználó Britta Simon szerint SAP Business objektumot felhőbe a felhasználóifiók-hozzáférés engedélyezése az Azure AD egyszeri bejelentkezéshez használandó.

SAP Business objektumot felhő Britta Simon hozzárendelése:

1. Az Azure portálon az alkalmazások nézet megnyitásához, és keresse meg a könyvtár nézet. Válassza ki **vállalati alkalmazások**, majd válassza ki **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SAP Business objektumot felhő**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. A bal oldali menüben válassza ki a **felhasználók és csoportok**.

    ![Felhasználók és csoportok kiválasztása][202] 

4. Válassza a **Hozzáadás** lehetőséget. Ezt követően a **hozzáadása hozzárendelés** lapon jelölje be **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása lap][203]

5. Az a **felhasználók és csoportok** lapra, jelölje be a felhasználók listáján **Britta Simon**.

6. Az a **felhasználók és csoportok** lapon jelölje be **válasszon**.

7. Az a **hozzáadása hozzárendelés** lapon jelölje be **hozzárendelése**.

![A felhasználói szerepkör hozzárendelése][200] 
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

Az SAP Business objektumot felhő csempe a hozzáférési panelen válassza ki, amikor be kell automatikusan jelentkeznie az SAP Business objektumot felhő alkalmazására.

A hozzáférési panel kapcsolatos további információkért lásd: [a hozzáférési panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png

