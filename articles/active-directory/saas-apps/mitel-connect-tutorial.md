---
title: 'Oktatóanyag: Mitel csatlakoztatása az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés és Mitel csatlakoztatása Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 611867cf5606d5a1078706e1c0f67f673a7fa500
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254599"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Oktatóanyag: Mitel MiCloud csatlakoztatása az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja a Mitel MiCloud csatlakozás integrálása az Azure Active Directory (Azure AD) lesz. Csatlakozás MiCloud integrálása az Azure AD nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a vállalati hitelesítő adataikkal MiCloud Connect alkalmazások Azure AD-ben.
* A fiókját, hogy a rendszer automatikusan bejelentkezett MiCloud csatlakozás (egyszeri bejelentkezés) a felhasználók engedélyezheti az Azure AD-fiókjukat.


Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

MiCloud csatlakozzon az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Azure AD-előfizetés

  Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Egy Mitel MiCloud csatlakozáshoz fiók

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban azt fogjuk tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés (SSO).

* Csatlakozás Mitel támogatja **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-mitel-connect-from-the-gallery"></a>Csatlakozás Mitel hozzáadása a katalógusból

Az Azure AD-be Mitel Connect integráció konfigurálásához, hozzá kell Mitel csatlakoztatása a katalógusból felügyelt SaaS-alkalmazások az Azure Portalon a listájára.

**Mitel csatlakozás hozzáadása a katalógusból, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portal](https://portal.azure.com)** bal oldali navigációs paneljén kattintson az **Azure Active Directory** elemre.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Kattintson a **vállalati alkalmazások** majd **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Kattintson a **új alkalmazás**.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. Típus **Mitel csatlakozás** a keresési mezőben, kattintson a **Mitel csatlakozás** az eredmények panelen, és kattintson **Hozzáadás**.

     ![Mitel csatlakozzon a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálhatja és MiCloud csatlakoztatása az Azure AD egyszeri bejelentkezés tesztelése alapján nevű tesztfelhasználó **Britta Simon**. Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó MiCloud csatlakozzon a hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az MiCloud Connect tesztelése és konfigurálása, meg kell a következő lépéseket:

1. **[MiCloud csatlakoztatása az Azure AD SSO konfigurálása](#configure-micloud-connect-for-sso-with-azure-ad)**  – ahhoz, hogy a felhasználók számára a szolgáltatás használatához és az alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
4. **[Hozzon létre egy Mitel MiCloud csatlakozás tesztfelhasználót](#create-a-mitel-micloud-connect-test-user)**  - a-megfelelője a Britta Simon, amely kapcsolódik a felhasználó Azure ad-ben reprezentációja MiCloud csatlakozás fiókja rendelkezik.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>MiCloud konfigurálása egyszeri bejelentkezéshez az Azure AD Connect

Ebben a szakaszban fog engedélyezése az Azure AD egyszeri bejelentkezés az MiCloud csatlakoztatása az Azure Portalon, és a csatlakozás MiCloud fiók konfigurálásával lehetővé teheti az Azure AD egyszeri bejelentkezés.

Szeretné konfigurálni MiCloud csatlakozás egyszeri bejelentkezés az Azure ad-hez, a legegyszerűbb, nyissa meg az Azure Portalon, és a Mitel fiókportál egymás mellett. Néhány információt másolása az Azure Portalról a Mitel fiókportál és az egyes a fiókportálról Mitel az Azure Portalra kell.


1. A konfigurációs weblap megnyitásához a [az Azure portal](https://portal.azure.com/), tegye a következőket:

    a. Az a **Mitel csatlakozás** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

    b. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson a **SAML**.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)
    
    Az SAML-alapú bejelentkezési oldal jelenik meg.

2. A konfiguráció párbeszédpanel megnyitásához a Mitel fiókportálon, tegye a következőket:

    a. Az a **Phone rendszer** menüben kattintson a **bővítmény szolgáltatásai**.

    b. Jobb oldalán **egyszeri bejelentkezés**, kattintson a **aktiválás** vagy **beállítások**.
    
    A csatlakozás egyszeri bejelentkezés beállításai párbeszédpanel jelenik meg.
    
3. Válassza ki a **engedélyezése egyszeri bejelentkezés** jelölőnégyzetet.
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. Az Azure Portalon kattintson a **szerkesztése** ikonra a **alapszintű SAML-konfigurációja** szakaszban.
    ![image](common/edit-urls.png)

    Az alapszintű SAML-konfigurációja párbeszédpanel jelenik meg.

5.  Másolja az URL-címet a **Mitel azonosító (entityid)** Mitel fiókportálon mezőt, és illessze be azt a **azonosító (entityid)** mezőt az Azure Portalon.

6. Másolja az URL-címet a **válasz URL-cím (helyességi feltétel fogyasztói szolgáltatás URL-címe)** Mitel fiókportálon mezőt, és illessze be azt a **válasz URL-cím (helyességi feltétel fogyasztói szolgáltatás URL-címe)** mezőt az Azure Portalon.  
   ![image](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. Az a **bejelentkezési URL-cím** beviteli mező, írja be a következő URL-címek egyikét:

    * **https://portal.shoretelsky.com** -a Mitel fiókportál használandó alapértelmezett Mitel alkalmazását
    * **https://teamwork.shoretel.com** -a csapatmunkát használja, mint az alapértelmezett Mitel alkalmazás

    **MEGJEGYZÉS**: Az alapértelmezett Mitel alkalmazás az alkalmazás érhető el, ha a felhasználó a hozzáférési panelen Mitel csatlakozás csempére kattint. Ez egyben az alkalmazás érhető el, amikor egy teszt beállítása az Azure ad-ből.

8. Kattintson a **mentése** a a **alapszintű SAML-konfigurációja** párbeszédpanel az Azure Portalon.

9. Az a **SAML-aláíró tanúsítvány** szakaszában a **SAML-alapú bejelentkezés** lapon az Azure Portalon, kattintson a **letöltése** melletti **tanúsítvány (Base64)** letöltéséhez a **aláíró tanúsítvány** , és mentse a számítógépre.
    ![image](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Nyissa meg az aláíró tanúsítvány fájlt egy szövegszerkesztőben, a fájl másolása az összes adat és illessze be az adatokat a **aláíró tanúsítvány** mezőt a Mitel fiókportálon. 
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. Az a **telepítő Mitel kapcsolatot** szakaszában a **SAML-alapú bejelentkezés** oldal az Azure Portal, tegye a következőket:

    a. Másolja az URL-címet a **bejelentkezési URL-cím** mezőt, és illessze be azt a **bejelentkezési URL** mezőt a Mitel fiókportálon.

    b. Másolja az URL-címet a **az Azure AD-azonosító** mezőt, és illessze be azt a **Entitásazonosító** mezőt a Mitel fiókportálon.
    ![image](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Kattintson a **mentése** a a **csatlakozás egyszeri bejelentkezési beállításainak** Mitel fiókportálon párbeszédpanel bezárásához.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory**, kattintson a **felhasználók**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Kattintson a **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai párbeszédpanelen tegye a következőket:

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be a **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a brittasimon @\<vállalati_tartomány\>.\< bővítmény\>.  
Például: BrittaSimon@contoso.com.

    c. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon Mitel kapcsolódni a hozzáférés biztosításával az Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon kattintson a **vállalati alkalmazások**, és kattintson a **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában kattintson **Mitel csatlakozás**.

    ![Az alkalmazások listáját a Mitel csatlakozás hivatkozás](common/all-applications.png)

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása**, majd kattintson a **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a a **felhasználók** listában, majd kattintson a **kiválasztása** a képernyő alján.

6. Az SAML-előfeltétel a várt szerepkör értéket, ha a felhasználó a megfelelő szerepkört válasszon a listából, az a **Szerepkörválasztás** párbeszédpanel, és kattintson **kiválasztása** a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Mitel MiCloud csatlakozás tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű MiCloud csatlakozás fiókja hoz létre. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

A Mitel fiókportálon felhasználók hozzáadásával kapcsolatos részletekért lásd: a [hozzáadása egy felhasználó](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) a Mitel Tudásbázis.

Hozzon létre egy felhasználót a MiCloud csatlakozáshoz fiók az alábbi adatokkal:

  * **név:** Britta Simon

* **Üzleti E-mail-címet:** `brittasimon@<yourcompanydomain>.<extension>`   
(Példa: [ brittasimon@contoso.com ](mailto:brittasimon@contoso.com))

* **Felhasználónév:** `brittasimon@<yourcompanydomain>.<extension> `  
(Példa: [ brittasimon@contoso.com ](mailto:brittasimon@contoso.com); a felhasználónév megegyezik a általában a felhasználó üzleti e-mail-címe)

**MEGJEGYZÉS:** A felhasználó MiCloud csatlakozáshoz felhasználónév a felhasználó e-mail-címre az Azure-ban azonosnak kell lennie.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni fogja az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen Mitel csatlakozás csempére kattint, a rendszer automatikusan átirányítja bejelentkezni az MiCloud Connect alkalmazás alapértelmezés szerint konfigurálta a **bejelentkezési URL-cím** mező. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
