---
title: 'Oktatóanyag: Azure Active Directory integráció a Mitel-csatlakozással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Mitel közötti kapcsolat között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 30a4dcbd15d8a47d99b3a61879a7e61938d3b133
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160534"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Oktatóanyag: Azure Active Directory integráció a Mitel MiCloud csatlakozással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Mitel MiCloud-kapcsolatot Azure Active Directory (Azure AD) szolgáltatással. Az MiCloud-kapcsolat Azure AD-vel való integrálásával az alábbi előnyökkel jár:

* Az Azure AD-ben beállíthatja, hogy a vállalati hitelesítő adataival Hogyan férhet hozzá a MiCloud-alkalmazásokhoz.
* A fiókjában lévő felhasználók számára engedélyezheti, hogy a rendszer automatikusan bejelentkezzen a MiCloud (egyszeri bejelentkezés) az Azure AD-fiókjával.


Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció MiCloud-kapcsolaton keresztüli konfigurálásához a következő elemek szükségesek:

* Azure AD-előfizetés

  Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Mitel MiCloud-csatlakozási fiók

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezést (SSO) konfigurálja és teszteli.

* Az Mitel-összekötő támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-mitel-connect-from-the-gallery"></a>Mitel-kapcsolat hozzáadása a gyűjteményből

A Mitel az Azure AD-be való csatlakozásának konfigurálásához hozzá kell adnia a Mitel csatlakozást a katalógusból a felügyelt SaaS-alkalmazások listájához a Azure Portal.

**A Mitel-kapcsolat a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. Az **[Azure Portal](https://portal.azure.com)** bal oldali navigációs paneljén kattintson az **Azure Active Directory** elemre.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Kattintson a **vállalati alkalmazások** elemre, majd **a minden alkalmazás**lehetőségre.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Kattintson az **új alkalmazás**lehetőségre.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. Írja be a **Mitel-kapcsolat** kifejezést a Keresés mezőbe, majd kattintson a **Mitel-kapcsolat** az eredmények panelen, majd a **Hozzáadás**gombra.

     ![Mitel-kapcsolat az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálhatja és tesztelheti a MiCloud-kapcsolaton keresztül egy **Britta Simon**nevű teszt felhasználó alapján. Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolatra van szükség a MiCloud-csatlakozásban.

Az Azure AD egyszeri bejelentkezés MiCloud-kapcsolaton keresztüli konfigurálásához és teszteléséhez a következő lépéseket kell elvégeznie:

1. **[Konfigurálja az MiCloud-hez való kapcsolódást az egyszeri bejelentkezéshez az Azure ad-vel](#configure-micloud-connect-for-sso-with-azure-ad)** – lehetővé teszi, hogy a felhasználók ezt a funkciót használják, és az egyszeri bejelentkezés beállításait az alkalmazás oldalán konfigurálja.
2. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
3. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
4. **[Hozzon létre egy Mitel MiCloud kapcsolódási teszt felhasználóval](#create-a-mitel-micloud-connect-test-user)** , hogy a felhasználó Azure ad-képviseletéhez kapcsolódó MiCloud-csatlakozási fiókhoz tartozó Britta Simon partnere legyen.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>MiCloud-kapcsolat konfigurálása egyszeri bejelentkezéshez az Azure AD-vel

Ebben a szakaszban engedélyezni fogja az Azure AD egyszeri bejelentkezést a MiCloud kapcsolódáshoz a Azure Portalban, és konfigurálja a MiCloud csatlakozási fiókját az egyszeri bejelentkezés engedélyezéséhez az Azure AD használatával.

A MiCloud az Azure AD-vel való összekapcsolásának konfigurálásához a legegyszerűbben a Azure Portal és a Mitel-fiók portálját kell megnyitnia egymás mellett. Néhány információt át kell másolnia a Azure Portalról a Mitel-fiók portálra, és néhányat a Mitel-fiók portálról a Azure Portalra.


1. A [Azure Portal](https://portal.azure.com/)konfigurációs oldalának megnyitásához tegye a következőket:

    a. Az **Mitel összekapcsolása** lapon kattintson az **egyszeri bejelentkezés**elemre.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

    b. Az **egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen kattintson az **SAML**elemre.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)
    
    Megjelenik az SAML-alapú bejelentkezési oldal.

2. A Mitel-fiók portálon a konfigurálás párbeszédpanel megnyitásához tegye a következőket:

    a. A **telefonos rendszer** menüben kattintson a **kiegészítő funkciók**elemre.

    b. Az **egyszeri bejelentkezéstől**jobbra kattintson az **aktiválás** vagy a **Beállítások**lehetőségre.
    
    Megjelenik az egyszeri bejelentkezési beállítások összekapcsolása párbeszédpanel.
    
3. Jelölje be az **egyszeri bejelentkezés engedélyezése** jelölőnégyzetet.
    ![rendszerkép](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. A Azure Portal kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** szakaszban.
    ![rendszerkép](common/edit-urls.png)

    Megjelenik az alapszintű SAML-konfiguráció párbeszédpanel.

5.  Másolja az URL-címet a Mitel-fiók portál Mitel-azonosító **(Entity ID)** mezőjéből, és illessze be a Azure Portal **azonosító (entitás azonosítója)** mezőjébe.

6. Másolja az URL-címet a válasz URL-címéből **(az állítási fogyasztói szolgáltatás URL-címe)** a Mitel-fiók portálon, és illessze be a **Válasz URL-címébe (a felhasználói szolgáltatás URL-címe)** mezőben a Azure Portal.  
   ![rendszerkép](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. A **bejelentkezési URL-cím** szövegmezőbe írja be az alábbi URL-címek egyikét:

    * **https://portal.shoretelsky.com** – a Mitel-fiók portál használata alapértelmezett Mitel-alkalmazásként
    * **https://teamwork.shoretel.com** – a csapatmunka alapértelmezett Mitel-alkalmazásként való használata

    **Megjegyzés**: az alapértelmezett Mitel-alkalmazás az az alkalmazás, amely akkor érhető el, amikor a felhasználó a hozzáférési panelen a Mitel csatlakozás csempére kattint. Ez az alkalmazás akkor is elérhető, amikor az Azure AD-ból tesztet telepít.

8. Kattintson a **Save (Mentés** ) gombra a Azure Portal **alapszintű SAML-konfiguráció** párbeszédpanelén.

9. A Azure Portal **SAML-alapú bejelentkezési** lapjának **SAML-aláíró tanúsítvány** szakaszában kattintson a **tanúsítvány (Base64)** melletti **Letöltés** elemre az **aláíró tanúsítvány** letöltéséhez és a számítógépbe való mentéséhez.
    ![rendszerkép](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Nyissa meg az aláíró tanúsítványfájl egy szövegszerkesztőben, másolja a fájlban található összes adatfájlt, majd illessze be az adattípust a Mitel-fiók portál **aláíró tanúsítvány** mezőjébe. 
    ![rendszerkép](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. A Azure Portal **SAML-alapú bejelentkezési** lapján a **Mitel-kapcsolat beállítása** szakaszban tegye a következőket:

    a. Másolja az URL-címet a **bejelentkezési URL** -cím mezőbe, és illessze be a **bejelentkezési URL** mezőbe a Mitel-fiók portálon.

    b. Másolja az URL-címet az **Azure ad-azonosító** mezőjéből, és illessze be a Mitel-fiók-portál **entitás-azonosító** mezőjébe.
    ![rendszerkép](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Kattintson a **Save (Mentés** ) gombra az **egyszeri bejelentkezési beállítások összekapcsolása** párbeszédpanel Mitel-fiók portálján.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ebben a szakaszban egy Britta Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal a bal oldali ablaktáblán kattintson az **Azure Active Directory**elemre, majd a **felhasználók**lehetőségre, végül pedig a **minden felhasználó**elemre.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Kattintson a képernyő tetején található **új felhasználó** lehetőségre.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai párbeszédpanelen hajtsa végre a következő lépéseket:

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A **név** mezőbe írja be a következőt: **BrittaSimon**.
  
    b. A **Felhasználónév** mezőbe írja be a következőt: brittasimon @\<yourcompanydomain\>.\<bővítmény\>.  
Például: BrittaSimon@contoso.com.

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.

    d. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Mitel kapcsolódáshoz.

1. A Azure Portal kattintson a **vállalati alkalmazások**elemre, majd kattintson a **minden alkalmazás**elemre.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában kattintson a **Mitel-kapcsolat**elemre.

    ![Az Mitel kapcsolódási hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben kattintson a **felhasználók és csoportok**elemre.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása**elemre, majd a **hozzárendelés hozzáadása** párbeszédpanelen a **felhasználók és csoportok** elemre.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a **felhasználók** listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben bármelyik szerepkör értékét várja, válassza ki a megfelelő szerepkört a felhasználó számára a **szerepkör kiválasztása** párbeszédpanelen, majd kattintson a **kiválasztás** gombra a képernyő alján.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés**elemre.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Mitel-MiCloud csatlakozási teszt felhasználó létrehozása

Ebben a szakaszban létrehoz egy Britta Simon nevű felhasználót a MiCloud-összekötő fiókjában. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

A felhasználók a Mitel-fiók portálon való hozzáadásával kapcsolatos részletekért tekintse meg a [felhasználói cikk hozzáadása](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) a Mitel Tudásbázisban című témakört.

Hozzon létre egy felhasználót a MiCloud csatlakozási fiókjában a következő részletekkel:

  * **Név:** Britta Simon

* **Üzleti e-mail-cím:** `brittasimon@<yourcompanydomain>.<extension>`   
(Példa: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Felhasználónév:** `brittasimon@<yourcompanydomain>.<extension>`  
(Példa: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); a felhasználó felhasználóneve általában ugyanaz, mint a felhasználó üzleti e-mail-címe)

**Megjegyzés:** A felhasználó MiCloud-kapcsolati felhasználónevének meg kell egyeznie a felhasználó e-mail-címével az Azure-ban.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával tesztelheti.

Ha a hozzáférési panelen a Mitel-csatlakozás csempére kattint, a rendszer automatikusan átirányítja a bejelentkezési **URL-cím** mezőben az alapértelmezettként konfigurált MiCloud-csatlakozási alkalmazásba való bejelentkezéshez. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
