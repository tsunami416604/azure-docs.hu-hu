---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező AnswerHub |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és AnswerHub között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e05f865f0fd76e93b92f3bb09f1babd780074065
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442579"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező AnswerHub

Ebben az oktatóanyagban elsajátíthatja, hogyan AnswerHub integrálása az Azure Active Directory (Azure AD).
Ezeket az előnyöket AnswerHub integrálása az Azure AD biztosítja:

* Az Azure AD, hogy ki férhet hozzá AnswerHub is használhatja.
* Hagyhatja, hogy a felhasználók automatikusan jelentkezhetnek be AnswerHub (egyszeri bejelentkezés) az Azure AD fiókjukkal.
* A fiókok egy központi helyen kezelheti: az Azure Portalon.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása AnswerHub, a következőkre lesz szüksége:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, elkezdheti a [egy havi próbalehetőség](https://azure.microsoft.com/pricing/free-trial/).
* Egyszeri bejelentkezés engedélyezve tartalmazó AnswerHub előfizetést.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* AnswerHub SP által kezdeményezett egyszeri Bejelentkezést támogatja.

## <a name="add-answerhub-from-the-gallery"></a>AnswerHub hozzáadása a katalógusból

AnswerHub integrálása az Azure AD beállításához, hozzá kell AnswerHub a katalógusból az felügyelt SaaS-alkalmazásokhoz.

**AnswerHub hozzáadása a katalógusból:**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory-gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Egy alkalmazás hozzáadásához válassza **új alkalmazás** az ablak tetején.

    ![Új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **AnswerHub**. Válassza ki **AnswerHub** a találatok listájában, és válassza ki a **Hozzáadás**.

     ![Az eredmények listájában AnswerHub](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Állítsa be, és az Azure AD egyszeri bejelentkezés tesztelése

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az AnswerHub Britta Simon nevű tesztfelhasználó használatával.
Az egyszeri bejelentkezés Azure AD-felhasználót és a megfelelő felhasználó közötti kapcsolatot létesít a AnswerHub kell.

Az Azure AD egyszeri bejelentkezés az AnswerHub tesztelése és konfigurálása, meg kell ezeket a feladatokat:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy a felhasználók a funkció használatához.
2. [AnswerHub egyszeri bejelentkezés konfigurálása](#configure-answerhub-single-sign-on) állíthatja be az egyszeri bejelentkezés beállításai az alkalmazás oldalán.
3. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) Britta Simon nevű.
4. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. Hozzon létre egy AnswerHub tesztfelhasználó, amely megfelel, és az Azure ad-ben tesztfelhasználó van csatolva.
6. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban beállíthatja az Azure AD egyszeri bejelentkezés az Azure Portalon.

**Az Azure AD egyszeri bejelentkezés konfigurálása az AnswerHub:**

1. Az a [az Azure portal](https://portal.azure.com/), az a **AnswerHub** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés gombot](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válasszon **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés válassza metódus párbeszédpanel](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon, a Szerkesztés ikonra nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel bezárásához.

    ![Állítsa be egyszeri bejelentkezést az SAML lap](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** területén kövesse az alábbi lépéseket:

    ![Alapszintű SAML-konfigurációja szakasz](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** mezőbe írjon be egy URL-címet, amelyen ez a minta: `https://<company>.answerhub.com`

    b. Az a **azonosító (entityid)** mezőbe írjon be egy URL-címet, amelyen ez a minta: `https://<company>.answerhub.com`

    > [!NOTE]
    > Ezek az értékek nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-cím és azonosító. Forduljon a [AnswerHub támogatási csapatának](mailto:success@answerhub.com) értékének lekéréséhez. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be a **letöltése** mellett kapcsolni **tanúsítvány (Base64)** , a igényeknek, és mentse el a tanúsítványt a számítógépen.

    ![Tanúsítvány letöltésére szolgáló hivatkozásra.](common/certificatebase64.png)

6. Az a **AnswerHub beállítása** területén másolja a megfelelő URL-címe vagy URL-címek, a követelmények alapján.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

   Másolhatja az URL-címek:
    - Bejelentkezési URL

    - Azure AD-azonosító

    - Kijelentkezési URL

### <a name="configure-answerhub-single-sign-on"></a>AnswerHub egyszeri bejelentkezés konfigurálása

Ebben a szakaszban állíthatja be egyszeri bejelentkezést a AnswerHub.  

**AnswerHub egyszeri bejelentkezés konfigurálása:**

1. Egy másik böngészőablakban jelentkezzen be a AnswerHub vállalati hely rendszergazdaként.

    > [!NOTE]
    > Ha AnswerHub segítségre van szüksége, forduljon a [AnswerHub támogatási csapatának](mailto:success@answerhub.com.).

2. Lépjen a **felügyeleti**.

3. Az a **felhasználók és csoportok** lapján, a bal oldali panelen, a **közösségi beállítások** szakaszban jelölje be **SAML-telepítő**.

4. Az a **Identitásszolgáltató konfigurációs** lapra, végezze el az alábbi lépéseket:

    ![Felhasználók és csoportok lapon](./media/answerhub-tutorial/ic785172.png "SAML-telepítő")  
  
    a. Az a **Identitásszolgáltató bejelentkezési URL-cím** mezőbe illessze be a **bejelentkezési URL-cím** , az Azure Portalról másolt.
  
    b. Az a **Identitásszolgáltató kijelentkezési URL-címe** mezőbe illessze be a **kijelentkezési URL-címe** , az Azure Portalról másolt.

    c. Az a **Identitásszolgáltató azonosító formátuma** mezőbe írja be a **azonosító** kijelölt érték a **felhasználói attribútumok** szakaszban az Azure Portalon.
  
    d. Válassza ki **kulcsok és tanúsítványok**.

5. Az a **kulcsok és tanúsítványok** szakaszban, a lépések végrehajtása:

    ![Kulcsok és tanúsítványok szakasz](./media/answerhub-tutorial/ic785173.png "kulcsok és tanúsítványok")  

    a. Nyissa meg a Jegyzettömbben az Azure Portalról letöltött Base64-kódolású tanúsítványt, másolja ki és illessze be a tartalmát a **Identitásszolgáltató nyilvános kulcs (x 509-formátum)** mezőbe.
  
    b. Kattintson a **Mentés** gombra.

6. Az a **Identitásszolgáltató konfigurációs** lapon jelölje be **mentése** újra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban hozzon létre egy tesztfelhasználót Britta Simon nevű az Azure Portalon.

**Az Azure ad-ben tesztfelhasználó létrehozása:**

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Válassza ki az Azure Active Directory, a felhasználók, az összes felhasználó](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az új felhasználó gomb](common/new-user.png)

3. A felhasználói tulajdonságok a lépések végrehajtásához.

    ![Felhasználói tulajdonságok](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be **brittasimon @< yourcompanydomain.extension >**.  
    Például: BrittaSimon@contoso.com.

    c. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban beállította Britta Simon számára a hozzáférés biztosításával AnswerHub az Azure AD egyszeri bejelentkezés használatára.

**Az Azure ad-ben tesztfelhasználó hozzárendelése:**

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**, majd válassza ki **AnswerHub**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **AnswerHub**.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza ki **felhasználó hozzáadása**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![Adja hozzá a hozzárendelési ablaktáblán](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a a **felhasználók** listában, és válassza ki a **kiválasztása** gomb alsó részén a képernyő.

6. Ha az a SAML-előfeltétel szerepkör értéket vár a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő szerepkört a felhasználóhoz a listából. 

7. Válassza ki a **kiválasztása** gombra a képernyő alján.

8. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.

### <a name="create-an-answerhub-test-user"></a>Hozzon létre egy AnswerHub tesztfelhasználó számára

Ahhoz, hogy az Azure AD-felhasználók AnswerHub bejelentkezni, hozzá kell őket a AnswerHub. AnswerHub Ez a feladat manuálisan történik.

**A felhasználói fiók beállítása:**

1. Jelentkezzen be a **AnswerHub** vállalati hely rendszergazdaként.

2. Lépjen a **felügyeleti**.

3. Válassza ki a **felhasználók és csoportok** fülre.

4. A bal oldali ablaktáblán az a **felhasználók kezelése** szakaszban jelölje be **felhasználók létrehozása vagy importálása**, majd válassza ki **felhasználók és csoportok**.

   ![Felhasználók és csoportok lapon](./media/answerhub-tutorial/ic785175.png "felhasználók és csoportok")

5. A megfelelő mezőkbe írja be a **E-mail-cím**, **felhasználónév**, és **jelszó** érvényes Azure AD-fiókot, hogy szeretné-e hozzá, és válassza ki **mentése** .

> [!NOTE]
> Bármely egyéb felhasználói fiók létrehozási eszköz is használhatja, vagy API Azure AD felhasználói fiók beállítása AnswerHub által biztosított.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

A AnswerHub csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a AnswerHub, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az Azure Active Directory SaaS-alkalmazások integrálását ismertető oktatóanyagok](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

