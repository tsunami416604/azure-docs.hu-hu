---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az AnswerHubbal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az AnswerHub között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a124832bd42a0a144ebc6000b818fb825aa422ef
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73152995"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Oktatóanyag: Az Azure Active Directory integrációja az AnswerHubbal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az AnswerHubot az Azure Active Directoryval (Azure AD).
Az AnswerHub integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá az AnswerHubhoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan jelentkezzen be az AnswerHub-ba az Azure AD-fiókjukkal (egyszeri bejelentkezés).
* A fiókokat egy központi helyről kezelheti: az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa [el az Alkalmazásokra való egyszeri bejelentkezés az Azure Active Directoryban című témakört.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció beállítása az AnswerHubhasználatával, a következőkre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, megkezdheti az [egyhónapos próbaverziót.](https://azure.microsoft.com/pricing/free-trial/)
* Olyan AnswerHub-előfizetés, amelynek engedélyezve van az egyszeri bejelentkezés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az AnswerHub támogatja az SP által kezdeményezett egyszeri felet.

## <a name="add-answerhub-from-the-gallery"></a>Az AnswerHub hozzáadása a galériából

Az AnswerHub azure AD-be való integrációjának beállításához hozzá kell adnia az AnswerHubot a katalógusból a felügyelt SaaS-alkalmazásokhoz.

**Az AnswerHub hozzáadása a galériából:**

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

3. Alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget az ablak tetején.

    ![Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az AnswerHub című **kifejezést.** Válassza az **AnswerHub** elemet az eredménylistában, majd kattintson a **Hozzáadás gombra.**

     ![Az AnswerHub az eredménylistában](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezésbeállítása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezést az AnswerHubbal egy Britta Simon nevű tesztfelhasználó használatával.
Egyszeri bejelentkezéshez létre kell hoznia egy kapcsolatot egy Azure AD-felhasználó és a megfelelő felhasználó az AnswerHubon.

Az Azure AD egyszeri bejelentkezése az AnswerHubhasználatával történő konfigurálásához és teszteléséhez el kell végeznie az alábbi feladatokat:

1. [Konfigurálja az Azure AD egyszeri bejelentkezést,](#configure-azure-ad-single-sign-on) hogy a felhasználók használhassák a funkciót.
2. [Konfigurálja az AnswerHub egyszeri bejelentkezési](#configure-answerhub-single-sign-on) beállításához az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. Hozzon létre egy Britta Simon nevű [Azure AD-tesztfelhasználót.](#create-an-azure-ad-test-user)
4. [Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy Britta Simon használhassa az Azure AD egyszeri bejelentkezést.
5. Hozzon létre egy AnswerHub-tesztfelhasználót, amely megfelel az Azure AD-tesztfelhasználónak, és kapcsolódik hozzá.
6. [Tesztelje az egyszeri bejelentkezést](#test-single-sign-on) a konfiguráció működésének ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést állít be az Azure Portalon.

**Az Azure AD egyszeri bejelentkezéskonfigurálása az AnswerHubhasználatával:**

1. Az [Azure Portalon](https://portal.azure.com/)az **AnswerHub-alkalmazásintegrációs** lapon válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezés gomb](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés a metódus kiválasztása párbeszédpanelen](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon jelölje ki a szerkesztés ikont az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszeri bejelentkezés beállítása SAML-lappal](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Alapvető SAML konfigurációs szakasz](common/sp-identifier.png)

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet, amely ezt a mintát rendelkezik:`https://<company>.answerhub.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet, amely rendelkezik ezzel a mintával:`https://<company>.answerhub.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba az [AnswerHub támogatási csapatával](mailto:success@answerhub.com) az értékek leéséhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában válassza a **Tanúsítvány (Base64)** melletti **Letöltés** hivatkozást, és mentse a tanúsítványt a számítógépre.

    ![Tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. Az **AnswerHub beállítása** szakaszban másolja a megfelelő URL-címet vagy URL-címeket a követelmények nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

   Ezeket az URL-címeket a következő kettnöke másolja:
    - Bejelentkezési URL

    - Azure Hirdetés-azonosító

    - Kijelentkezés URL-címe

### <a name="configure-answerhub-single-sign-on"></a>Az AnswerHub egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban egyetlen bejelentkezést állít be az AnswerHubhoz.  

**Az AnswerHub egyszeri bejelentkezéskonfigurálása:**

1. Egy másik böngészőablakban jelentkezzen be az AnswerHub vállalati webhelyére rendszergazdaként.

    > [!NOTE]
    > Ha segítségre van szüksége az AnswerHub konfigurálásához, forduljon az [AnswerHub támogatási csapatához.](mailto:success@answerhub.com.)

2. Nyissa meg a **Közigazgatás**.

3. A **Felhasználó és csoportok** lap bal oldali ablaktáblájának **Közösségi beállítások** csoportjában válassza az **SAML-beállítás**lehetőséget.

4. Az **IDP Config** lapon hajtsa végre az alábbi lépéseket:

    ![Felhasználók & Csoportok lapon](./media/answerhub-tutorial/ic785172.png "SAML beállítása")  
  
    a. Az **IDP-bejelentkezési URL-címmezőbe** illessze be az Azure Portalról másolt **bejelentkezési URL-címet.**
  
    b. Az **IDP-kijelentkezés URL-címe** mezőbe illessze be az Azure Portalról másolt **kijelentkezési URL-címet.**

    c. Az **IDP-névazonosító formátuma** mezőbe írja be az Azure Portal **Felhasználói attribútumok** szakaszában kijelölt **azonosító** értéket.
  
    d. Válassza a **Kulcsok és tanúsítványok lehetőséget.**

5. A **Kulcsok és tanúsítványok** szakaszban hajtsa végre az alábbi lépéseket:

    ![Kulcsok és tanúsítványok szakasz](./media/answerhub-tutorial/ic785173.png "Kulcsok és tanúsítványok")  

    a. Nyissa meg az Azure Portalról a Jegyzettömbben letöltött Base64-kódolású tanúsítványt, másolja a tartalmát, majd illessze be a tartalmat az **IDP nyilvános kulcs (x509 formátum)** mezőbe.
  
    b. Kattintson a **Mentés** gombra.

6. Az **IDP Config** lapon kattintson ismét a **Mentés gombra.**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy britta Simon nevű tesztfelhasználót az Azure Portalon.

**Azure AD-tesztfelhasználó létrehozása:**

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![Válassza ki az Azure Active Directoryt, a Felhasználók, az Összes felhasználót](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználói tulajdonságokban hajtsa végre ezeket a lépéseket.

    ![Felhasználói tulajdonságok](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be **a\@ brittasimon<a vállalattartomány.extension>**.  
    Például: BrittaSimon@contoso.com.

    c. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban beállíthatja a felhasználó Britta Simon azure AD egyszeri bejelentkezés használatával a felhasználói hozzáférést az AnswerHub.

**Az Azure AD-tesztfelhasználó hozzárendelése:**

1. Az Azure Portalon válassza a **Nagyvállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd az **AnswerHub**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **AnswerHub**lehetőséget.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** **párbeszédpanelen** a Felhasználók és csoportok lehetőséget.

    ![Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a **Felhasználók** listában, majd a képernyő alján található **Kijelölés** gomb lehetőséget.

6. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. 

7. Válassza a **Kijelölés** gombot a képernyő alján.

8. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

### <a name="create-an-answerhub-test-user"></a>AnswerHub-tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek az AnswerHubba, hozzá kell adnia őket az AnswerHubban. Az AnswerHubban ez a feladat manuálisan történik.

**Felhasználói fiók beállítása:**

1. Jelentkezzen be az **AnswerHub** vállalati webhelyére rendszergazdaként.

2. Nyissa meg a **Közigazgatás**.

3. Válassza a **Felhasználók & csoportok** lapot.

4. A bal oldali ablaktáblában a **Felhasználók kezelése** csoportban válassza a **Felhasználók létrehozása vagy importálása**lehetőséget, majd a **Felhasználók & csoport**lehetőséget.

   ![Felhasználók & Csoportok lapon](./media/answerhub-tutorial/ic785175.png "Felhasználók & csoportok")

5. A megfelelő mezőkben adja meg a hozzáadni kívánt érvényes Azure AD-fiók **e-mail címét**, **felhasználónevét**és **jelszavát,** majd válassza a **Mentés gombot.**

> [!NOTE]
> Az AnswerHub által biztosított bármely más felhasználói fiókkészítő eszköz vagy API segítségével azure AD-felhasználói fiókokat állíthat be.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelhasználatával teszteli.

Amikor kiválasztja az AnswerHub csempét a hozzáférési panelen, automatikusan be kell jelentkeznie az AnswerHubba, amelyhez az SSO-t beállította. A hozzáférési panelről további információt a [Bevezetés a hozzáférési panelbe című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Oktatóanyagok a SaaS-alkalmazások Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

