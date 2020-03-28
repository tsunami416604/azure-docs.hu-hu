---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a CloudPassage-szal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a CloudPassage között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0bcf0b4781e7ecb4301672f511a9f01f7fd2082
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561223"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a CloudPassage-szal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a CloudPassage-t az Azure Active Directoryval (Azure AD). Ha integrálja a CloudPassage-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a CloudPassage.Control in Azure AD who has access to CloudPassage.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkeznek a CloudPassage-ba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* CloudPassage egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A CloudPassage támogatja az **SP** által kezdeményezett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-cloudpassage-from-the-gallery"></a>CloudPassage hozzáadása a galériából

A CloudPassage azure AD-be való integrálásának konfigurálásához hozzá kell adnia a CloudPassage-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **CloudPassage** kifejezést a keresőmezőbe.
1. Válassza a **CloudPassage** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a CloudPassage-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a CloudPassage segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a CloudPassage-ban.

Az Azure AD SSO konfigurálásához és teszteléséhez a CloudPassage segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja CloudPassage SSO](#configure-cloudpassage-sso)** -konfigurálása az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
    1. **[Hozzon létre CloudPassage tesztfelhasználót](#create-cloudpassage-test-user)** – b.Simon megfelelőjével a CloudPassage-ban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **CloudPassage** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

     a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://portal.cloudpassage.com/saml/init/accountid`

    b. A **Válasz URL-cím** mezőjébe írjon be `https://portal.cloudpassage.com/saml/consume/accountid`egy URL-címet a következő minta használatával: . Ehhez az attribútumhoz tartozó értéket az **Egyszeri bejelentkezés beállítási dokumentációjára** kattintva szerezheti be a CloudPassage **portál Egyszeri bejelentkezési beállítások** szakaszában.

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és a válasz URL-címével. Lépjen kapcsolatba [a CloudPassage ügyféltámogatási csapatával,](https://www.cloudpassage.com/company/contact/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. A CloudPassage alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a CloudPassage alkalmazás azt várja, hogy néhány további attribútumot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmény nek megfelelően.

    | Név | Forrás attribútuma|
    | ---------------| --------------- |
    | Utónév |user.givenname |
    | Vezetéknév |user.vezetéknév |
    | e-mail |user.mail |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **CloudPassage beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával a CloudPassage hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **CloudPassage**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-cloudpassage-sso"></a>CloudPassage sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a CloudPassage vállalati webhelyére rendszergazdaként.

1. A felső menüben kattintson a **Beállítások**menü **Webhely felügyelete parancsára.** 
   
    ![Egyszeri bejelentkezés konfigurálása][12]

1. Kattintson a **Hitelesítési beállítások** fülre. 
   
    ![Egyszeri bejelentkezés konfigurálása][13]

1. Az **Egyszeri bejelentkezés beállításai** csoportban hajtsa végre az alábbi lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurálása][14]

    a. Jelölje **be az Egyszeri bejelentkezés engedélyezése(Egyszeri bejelentkezés) (Egyszeri bejelentkezés telepítési dokumentációja)** jelölőnégyzetet.
    
    b. Illessze be az **Azure AD-azonosítót** az **SAML kiállító URL-címmezőjébe.**
  
    c. Illessze be **a bejelentkezési URL-címet** az **SAML végpont URL-címmezőjébe.**
  
    d. A **Kijelentkezés URL-címének** beillesztése a **Kijelentkezés céloldalának** szövegmezőjébe.
  
    e. Nyissa meg a letöltött tanúsítványt a jegyzettömbben, másolja a letöltött tanúsítvány tartalmát a vágólapra, majd illessze be az **x 509 tanúsítvány** szövegdobozába.
  
    f. Kattintson a **Mentés** gombra.

### <a name="create-cloudpassage-test-user"></a>CloudPassage tesztfelhasználó létrehozása

Ez a szakasz célja egy B.Simon nevű felhasználó létrehozása a CloudPassage-ban.

**B.Simon nevű felhasználó létrehozásához a CloudPassage-ban hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be **a CloudPassage** vállalati webhelyére rendszergazdaként. 

1. A felső eszköztáron kattintson a **Beállítások**gombra, majd a **Webhely felügyelete parancsra.** 
   
    ![CloudPassage-tesztfelhasználó létrehozása][22] 

1. Kattintson a **Felhasználók** fülre, majd az **Új felhasználó hozzáadása gombra.** 
   
    ![CloudPassage-tesztfelhasználó létrehozása][23]

1. Az **Új felhasználó hozzáadása** csoportban hajtsa végre az alábbi lépéseket: 
   
    ![CloudPassage-tesztfelhasználó létrehozása][24]
    
    a. Az **Utónév** mezőbe írja be a Britta (Utónév) mezőbe. 
  
    b. A **Vezetéknév** mezőbe írja be a Simon (Simon) mezőbe.
  
    c. A **Felhasználónév** szövegmezőbe, az **E-mail** szövegmezőbe és az **E-mail újraírása** mezőbe írja be a Britta felhasználónevét az Azure AD-be.
  
    d. **Access Type néven**válassza a Halo portál **elérésének engedélyezése**lehetőséget.
  
    e. Kattintson a **Hozzáadás** gombra.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a CloudPassage csempére kattint, automatikusan be kell jelentkeznie arra a CloudPassage-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a CloudPassage szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

