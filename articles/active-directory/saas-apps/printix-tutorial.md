---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Printix-szel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Printix között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4aea7320-b2d5-49e0-9b63-aeaff0f6fe66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e883833f7998c073b574c892ed5c7777e01faab4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "62111463"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Oktatóanyag: Az Azure Active Directory integrációja a Printix-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Printixet az Azure Active Directoryval (Azure AD).

A Printix integrálása az Azure AD-vel a következő előnyöket nyújtja:

- Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Printix-hez
- Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve a Printix (Single Sign-On) az Azure AD-fiókok
- Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, tekintse meg, [hogy mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval.](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció nyomtatásához a következő elemekre van szükség:

- Azure AD-előfizetés
- Printix egyszeri bejelentkezésre engedélyezett előfizetés

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem javasoljuk éles környezet használatát.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi ajánlásokat:

- Ne használja az éles környezetet, kivéve, ha ez szükséges.
- Ha nem rendelkezik Azure AD próbakörnyezettel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos próbaverziót kaphat.

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben. Az oktatóanyagban ismertetett forgatókönyv két fő építőelemből áll:

1. Printix hozzáadása a galériából
1. Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

## <a name="adding-printix-from-the-gallery"></a>Printix hozzáadása a galériából
A Printix Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Printix-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Printixet a katalógusból szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára. 

    ![Active Directory][1]

1. Nyissa meg a **Vállalati alkalmazások at.** Akkor megy **Minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Alkalmazások][3]

1. A keresőmezőbe írja be a **Printix**parancsot.

    ![Azure AD-tesztfelhasználó létrehozása](./media/printix-tutorial/tutorial_printix_search.png)

1. Az eredménypanelen válassza a **Printix**lehetőséget, majd az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

    ![Azure AD-tesztfelhasználó létrehozása](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése
Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést a Printix-szel a "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés a munka, az Azure AD tudnia kell, hogy mi a megfelelő felhasználó Printix az Azure AD felhasználó számára. Más szóval létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Printix-ben.

A Printix ben rendelje hozzá a **felhasználónév** értékét az Azure AD-ben a **felhasználónév** értékeként a kapcsolat létrehozásához.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Printix-szel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálása az Azure AD egyszeri bejelentkezés](#configuring-azure-ad-single-sign-on)** – a felhasználók számára, hogy ezt a funkciót.
1. **[Azure AD-tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
1. **[Printix-tesztfelhasználó létrehozása](#creating-a-printix-test-user)** – britta Simon megfelelője a Printix ben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[Az Azure AD-tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)** – annak érdekében, hogy Britta Simon az Azure AD egyszeri bejelentkezést használhasson.
1. **[Az egyszeri bejelentkezés tesztelése](#testing-single-sign-on)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon, és konfigurálja az egyszeri bejelentkezést a Printix-alkalmazásban.

**Az Azure AD egyszeri bejelentkezésének nyomtatással történő konfigurálásához hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon a **Printix** alkalmazásintegrációs lapon kattintson az **Egyszeri bejelentkezés gombra.**

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az **egyszeri bejelentkezés** párbeszédpanelen válassza a **Mód** **SAML-alapú bejelentkezés** lehetőséget az egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. A **Printix tartomány és URL-címek szakaszban hajtsa** végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/printix-tutorial/tutorial_printix_url.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.printix.net`

    > [!NOTE] 
    > Az érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a Printix ügyféltámogatási csapatával](mailto:support@printix.net) az érték lefelvételéhez. 
 
1. Az **SAML aláíró tanúsítvány csoportban** kattintson a **Metaadat-XML** elemre, majd mentse a metaadatfájlt a számítógépre.

    ![Egyszeri bejelentkezés konfigurálása](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/printix-tutorial/tutorial_general_400.png)

1. Jelentkezzen be a Printix-bérlőhöz rendszergazdaként.

1. A felső menüben kattintson a jobb felső sarokban lévő ikonra, és válassza a "**Hitelesítés**" lehetőséget.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/printix-tutorial/tutorial_printix_06.png)

1. A **Telepítés** lapon válassza az **Azure/Office 365-hitelesítés engedélyezése** lehetőséget.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/printix-tutorial/tutorial_printix_07.png)

1. Az **Azure** lapon a bemeneti összevonásmetaadat-URL-cím az "**összevonási metaadat-dokumentum**" szövegmezőjébe. 

    Csatolja az Azure AD-ből letöltött metaadat-XML-fájlt a [Printix támogatási csapatához.](mailto:support@printix.net) Ezután feltöltik az XML-fájlt, és megadják az összevonási metaadat-URL-címet.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Kattintson a **"Teszt**" gombra, majd az **"OK"** gombra, ha a teszt sikeres volt.
   
     Az Azure active directory lapja a **tesztgombra** való kattintás után jelenik meg. "A teszt sikeres volt" itt azt jelenti, hogy az Azure-tesztfiók hitelesítő adatainak megadása után megjelenik egy üzenet " Beállítások tesztelt OK". Ezután kattintson az **OK** gombra.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/printix-tutorial/tutorial_printix_09.png)

1. Kattintson a **Mentés** gombra a **"Hitelesítés"** lapon.


> [!TIP]
> Az alkalmazás beállítása közben az [Azure Portalon](https://portal.azure.com)belül elolvashatja ezen utasítások tömör verzióját!  Miután hozzáadta ezt az alkalmazást az **Active Directory > Enterprise Applications** szakaszból, egyszerűen kattintson az Egyszeri **bejelentkezés** fülre, és az alsó **konfigurációs** szakaszon keresztül érheti el a beágyazott dokumentációt. A beágyazott dokumentációs funkcióról itt olvashat bővebben: [Az Azure AD beágyazott dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása
Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

![Azure AD-felhasználó létrehozása][100]

**Ha tesztfelhasználót szeretne létrehozni az Azure AD-ben, hajtsa végre a következő lépéseket:**

1. Az **Azure Portalon**a bal oldali navigációs ablakban kattintson az **Azure Active Directory** ikonjára.

    ![Azure AD-tesztfelhasználó létrehozása](./media/printix-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez nyissa meg a **Felhasználók és csoportok** lapot, és kattintson a Minden felhasználó **elemre.**
    
    ![Azure AD-tesztfelhasználó létrehozása](./media/printix-tutorial/create_aaduser_02.png) 

1. A **Felhasználó** párbeszédpanel megnyitásához kattintson a párbeszédpanel tetején a **Hozzáadás** gombra.
 
    ![Azure AD-tesztfelhasználó létrehozása](./media/printix-tutorial/create_aaduser_03.png) 

1. A **Felhasználó** párbeszédpanelen hajtsa végre az alábbi lépéseket:
 
    ![Azure AD-tesztfelhasználó létrehozása](./media/printix-tutorial/create_aaduser_04.png) 

    a. A **Név** mezőbe írja be **a BrittaSimon (BrittaSimon)** mezőbe.

    b. A **Felhasználónév** mezőbe írja be BrittaSimon **e-mail címét.**

    c. Válassza **a Jelszó megjelenítése** lehetőséget, és írja le a **Jelszó**értékét.

    d. Kattintson **a Létrehozás gombra.**
 
### <a name="creating-a-printix-test-user"></a>Printix-tesztfelhasználó létrehozása

A cél ebben a szakaszban, hogy hozzon létre egy felhasználó nevű Britta Simon a Printix. A Printix támogatja a just-in-time kiépítést, amely alapértelmezés szerint engedélyezve van.

Ebben a szakaszban nincs műveletelem. Új felhasználó jön létre a Printix elérésére tett kísérlet során, ha még nem létezik. 

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, kapcsolatba kell lépnie a [Printix támogatási csapatával.](mailto:support@printix.net)
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti Britta Simon számára az Azure egyszeri bejelentkezést a Printix-hozzáférés biztosításával.

![Felhasználó hozzárendelése][200] 

**Ha Britta Simont szeretné hozzárendelni a Printixhez, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások nézetét, majd keresse meg a címtárnézetet, és nyissa meg a **Vállalati alkalmazások,** majd kattintson a **Minden alkalmazás**elemre .

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában válassza a **Printix**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/printix-tutorial/tutorial_printix_app.png) 

1. A bal oldali menüben kattintson a **Felhasználók és csoportok**parancsra.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson **a Hozzáadás** gombra. Ezután válassza a **Felhasználók és csoportok** lehetőséget a Hozzárendelés **hozzáadása** párbeszédpanelen.

    ![Felhasználó hozzárendelése][203]

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a Felhasználók listában.

1. Kattintson a **Kijelölés** gombra a **Felhasználók és csoportok** párbeszédpanelen.

1. Kattintson a **Hozzárendelés** **gombra** a Hozzárendelés hozzáadása párbeszédpanelen.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Printix csempére kattint, automatikusan bejelentkezik a Printix alkalmazásba.

## <a name="additional-resources"></a>További források

* [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/printix-tutorial/tutorial_general_01.png
[2]: ./media/printix-tutorial/tutorial_general_02.png
[3]: ./media/printix-tutorial/tutorial_general_03.png
[4]: ./media/printix-tutorial/tutorial_general_04.png

[100]: ./media/printix-tutorial/tutorial_general_100.png

[200]: ./media/printix-tutorial/tutorial_general_200.png
[201]: ./media/printix-tutorial/tutorial_general_201.png
[202]: ./media/printix-tutorial/tutorial_general_202.png
[203]: ./media/printix-tutorial/tutorial_general_203.png

