---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Jobscience szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Jobscience között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f0ea5b922b2c958aabf5be3a6123bb81a8f0234
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048511"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Oktatóanyag: Az Azure Active Directory integrációja a Jobscience szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Jobscience-t az Azure Active Directoryval (Azure AD).

A Jobscience integrálása az Azure AD-vel a következő előnyöket nyújtja:

- Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Jobscience-hez
- Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jelentkezzenek be a Jobscience (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal
- Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, tekintse meg, [hogy mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval.](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Jobscience szolgáltatással a következő elemekre van szükség:

- Azure AD-előfizetés
- A Jobscience egyszeri bejelentkezésre engedélyezett előfizetés

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem javasoljuk éles környezet használatát.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi ajánlásokat:

- Ne használja az éles környezetet, kivéve, ha ez szükséges.
- Ha nem rendelkezik Azure AD próbakörnyezettel, itt egy hónapos próbaverziót kaphat: [Próbaajánlat.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben. Az oktatóanyagban ismertetett forgatókönyv két fő építőelemből áll:

1. Jobscience hozzáadása a galériából
1. Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

## <a name="adding-jobscience-from-the-gallery"></a>Jobscience hozzáadása a galériából
A Jobscience azure AD-be való integrálásának konfigurálásához hozzá kell adnia a feladattudományt a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a katalógusból szeretné hozzáadni a Jobscience-t, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára. 

    ![Active Directory][1]

1. Nyissa meg a **Vállalati alkalmazások at.** Akkor megy **Minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Alkalmazások][3]

1. A keresőmezőbe írja be a **Jobscience (Feladattudomány) kifejezést.**

    ![Azure AD-tesztfelhasználó létrehozása](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. Az eredménypanelen válassza a **Feladattudomány**lehetőséget, majd az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

    ![Azure AD-tesztfelhasználó létrehozása](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése
Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezését a Jobscience-tel egy "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés a munka, az Azure AD tudnia kell, hogy mi a megfelelő felhasználó jobscience az Azure AD-ben egy felhasználó számára. Más szóval létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Feladattudományi létre kell hozni.

A Feladattudományi, rendelje hozzá a **felhasználónév** értékét az Azure AD-ben, mint a **felhasználónév** a kapcsolat létrehozásához.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Jobscience szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálása az Azure AD egyszeri bejelentkezés](#configuring-azure-ad-single-sign-on)** – a felhasználók számára, hogy ezt a funkciót.
1. **[Azure AD-tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
1. **[Jobscience tesztfelhasználó létrehozása](#creating-a-jobscience-test-user)** – britta Simon megfelelője a Jobscience-ben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[Az Azure AD-tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)** – annak érdekében, hogy Britta Simon az Azure AD egyszeri bejelentkezést használhasson.
1. **[Az egyszeri bejelentkezés tesztelése](#testing-single-sign-on)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon, és konfigurálja az egyszeri bejelentkezést a Jobscience alkalmazásban.

**Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. Az Azure Portalon a **Jobscience** alkalmazásintegrációs lapon kattintson az **Egyszeri bejelentkezés gombra.**

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az **egyszeri bejelentkezés** párbeszédpanelen válassza a **Mód** **SAML-alapú bejelentkezés** lehetőséget az egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. A **Feladattudományi tartomány és AZ URL-címek szakaszban hajtsa** végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Ez az érték nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Ezt az értéket [a Jobscience Ügyfél támogatási csapat,](https://www.jobscience.com/support) vagy az SSO-profilt hoz létre, amely később az oktatóanyag ismerteti. 
 
1. Az **SAML aláíró tanúsítvány csoportban** kattintson a **Tanúsítvány (Base64)** elemre, majd mentse a tanúsítványfájlt a számítógépre.

    ![Egyszeri bejelentkezés konfigurálása](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/jobscience-tutorial/tutorial_general_400.png)

1. A **Feladattudományi konfiguráció csoportban** kattintson a **Feladattudomány konfigurálása gombra** a **Bejelentkezéskonfigurálásablak** megnyitásához. Másolja a **kijelentkezési URL-címet, az SAML entitásazonosítót és az SAML egyszeri bejelentkezési szolgáltatás URL-címét** a **rövid útmutató szakaszból.**

    ![Egyszeri bejelentkezés konfigurálása](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Jelentkezzen be a Jobscience vállalati webhelyére rendszergazdaként.

1. Nyissa meg a **telepítőt.**
   
   ![Telepítés](./media/jobscience-tutorial/IC784358.png "Telepítés")

1. A bal oldali navigációs ablak **Felügyeleti** szakaszában kattintson a **Tartománykezelés gombra** a kapcsolódó szakasz kibontásához, majd a **Saját tartomány** elemre kattintva nyissa meg a Saját **tartomány** lapot. 
   
   ![Saját tartomány](./media/jobscience-tutorial/ic767825.png "Saját tartomány")

1. Annak ellenőrzéséhez, hogy a tartomány megfelelően van-e beállítva, ellenőrizze, hogy az**My Domain Settings**a "**4.**

    ![Felhasználóra telepített tartomány](./media/jobscience-tutorial/ic784377.png "Felhasználóra telepített tartomány")

1. A Feladattudományi vállalat webhelyén kattintson a **Biztonsági vezérlők**, majd **az Egyszeri bejelentkezési beállítások parancsra.**
    
    ![Biztonsági vezérlők](./media/jobscience-tutorial/ic784364.png "Biztonsági vezérlők")

1. Az **Egyszeri bejelentkezés beállításai** csoportban hajtsa végre az alábbi lépéseket:
    
    ![Egyszeri bejelentkezés beállításai](./media/jobscience-tutorial/ic781026.png "Egyszeri bejelentkezés beállításai")
    
    a. Válassza **az SAML Enabled lehetőséget.**

    b. Kattintson **az Új gombra.**

1. Az **SAML Egyszeri bejelentkezés beállítása szerkesztés párbeszédpanelen** hajtsa végre az alábbi lépéseket:
    
    ![SAML egyszeri bejelentkezési beállítás](./media/jobscience-tutorial/ic784365.png "SAML egyszeri bejelentkezési beállítás")
    
    a. A **Név** mezőbe írja be a konfiguráció nevét.

    b. A **Kiállító** szövegmezőbe illessze be az Azure Portalról másolt **SAML-entitásazonosító**értékét.

    c. Az **Entitásazonosító** szövegmezőbe írja be a következőt:`https://salesforce-jobscience.com`

    d. Kattintson **a Tallózás** gombra az Azure AD-tanúsítvány feltöltéséhez.

    e. **Saml identity type (SAML identity type)** néven jelölje be a **Helyességi feltétel a Felhasználói objektum összevonási azonosítóját.**

    f. Saml **identity location (SAML identitáshely)** ként válassza az Identitás elem ét **a Tárgy utasítás NameIdentfier elemében**elemet.

    g. Az **Identitásszolgáltató bejelentkezési URL-címmezőjébe** illessze be az **SamL Single Sign-On Service URL-címének**értékét, amelyet az Azure Portalról másolt.

    h. Az **Identitásszolgáltató kijelentkezési URL-címmezőjébe** illessze be a **Kijelentkezési URL értékét,** amelyet az Azure Portalról másolt.

    i. Kattintson a **Mentés** gombra.

1. A bal oldali navigációs ablak **Felügyeleti** szakaszában kattintson a **Tartománykezelés gombra** a kapcsolódó szakasz kibontásához, majd a **Saját tartomány** elemre kattintva nyissa meg a Saját **tartomány** lapot. 
    
    ![Saját tartomány](./media/jobscience-tutorial/ic767825.png "Saját tartomány")

1. A **Saját tartomány** lap **Bejelentkezési lap márkajelzése** csoportban kattintson a **Szerkesztés gombra.**
    
    ![Bejelentkezési oldal márkajelzése](./media/jobscience-tutorial/ic767826.png "Bejelentkezési oldal márkajelzése")

1. A **Bejelentkezési lap márkajelzése** lap **Hitelesítési szolgáltatás** szakaszában megjelenik az **SAML SSO-beállítások** neve. Jelölje ki, majd kattintson a **Mentés gombra.**
    
    ![Bejelentkezési oldal márkajelzése](./media/jobscience-tutorial/ic784366.png "Bejelentkezési oldal márkajelzése")

1. Az SP által kezdeményezett egyszeri bejelentkezési URL-cím hez kattintson a **Biztonsági vezérlők** **menürész Egyszeri bejelentkezési beállításaira.**

    ![Biztonsági vezérlők](./media/jobscience-tutorial/ic784368.png "Biztonsági vezérlők")
    
    Kattintson a fenti lépésben létrehozott SSO-profilra. Ezen a lapon a vállalat egyszeri bejelentkezési `https://companyname.my.salesforce.com?so=companyid`URL-címe látható (például .    

> [!TIP]
> Az alkalmazás beállítása közben az [Azure Portalon](https://portal.azure.com)belül elolvashatja ezen utasítások tömör verzióját!  Miután hozzáadta ezt az alkalmazást az **Active Directory > Enterprise Applications** szakaszból, egyszerűen kattintson az Egyszeri **bejelentkezés** fülre, és az alsó **konfigurációs** szakaszon keresztül érheti el a beágyazott dokumentációt. A beágyazott dokumentációs funkcióról itt olvashat bővebben: [Az Azure AD beágyazott dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása
Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

![Azure AD-felhasználó létrehozása][100]

**Ha tesztfelhasználót szeretne létrehozni az Azure AD-ben, hajtsa végre a következő lépéseket:**

1. Az **Azure Portalon**a bal oldali navigációs ablakban kattintson az **Azure Active Directory** ikonjára.

    ![Azure AD-tesztfelhasználó létrehozása](./media/jobscience-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez nyissa meg a **Felhasználók és csoportok** lapot, és kattintson a Minden felhasználó **elemre.**
    
    ![Azure AD-tesztfelhasználó létrehozása](./media/jobscience-tutorial/create_aaduser_02.png) 

1. A **Felhasználó** párbeszédpanel megnyitásához kattintson a párbeszédpanel tetején a **Hozzáadás** gombra.
 
    ![Azure AD-tesztfelhasználó létrehozása](./media/jobscience-tutorial/create_aaduser_03.png) 

1. A **Felhasználó** párbeszédpanelen hajtsa végre az alábbi lépéseket:
 
    ![Azure AD-tesztfelhasználó létrehozása](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. A **Név** mezőbe írja be **a BrittaSimon (BrittaSimon)** mezőbe.

    b. A **Felhasználónév** mezőbe írja be BrittaSimon **e-mail címét.**

    c. Válassza **a Jelszó megjelenítése** lehetőséget, és írja le a **Jelszó**értékét.

    d. Kattintson **a Létrehozás gombra.**
 
### <a name="creating-a-jobscience-test-user"></a>Feladattudományi tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a Jobscience-be, ki kell építeni őket a Jobscience-be. Jobscience esetén kiépítése manuális feladat.

>[!NOTE]
>Az Azure Active Directory felhasználói fiókok kiépítéséhez bármely más Jobscience felhasználói fiók létrehozási eszköz vagy API-k segítségével azure Active Directory felhasználói fiókok kiépítése.
>  
        
**A felhasználói kiépítés konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be **a Jobscience** vállalati webhelyére rendszergazdaként.

1. Nyissa meg a telepítőt.
   
   ![Telepítés](./media/jobscience-tutorial/ic784358.png "Telepítés")
1. Nyissa meg a **Felhasználók \> kezelése lehetőséget.**
   
   ![Felhasználók](./media/jobscience-tutorial/ic784369.png "Felhasználók")
1. Kattintson **az Új felhasználó gombra.**
   
   ![Minden felhasználó](./media/jobscience-tutorial/ic784370.png "Minden felhasználó")
1. A **Felhasználó szerkesztése** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
   ![Felhasználói szerkesztés](./media/jobscience-tutorial/ic784371.png "Felhasználói szerkesztés")
   
   a. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például Britta.
   
   b. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például Simont.
   
   c. Az **Alias** mezőbe írja be a felhasználó aliasnevét, például brittas.

   d. Az **E-mail** mezőbe írja be a Brittasimon@contoso.comfelhasználó e-mail címét, például .

   e. A **Felhasználónév** mezőbe írja be a felhasználó Brittasimon@contoso.comnevét, például .

   f. A **Nick név** mezőbe írja be a felhasználó nicknevét, például Simont.

   g. Kattintson a **Mentés** gombra.

    
> [!NOTE]
> Az Azure Active Directory-fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követ, hogy erősítse meg a fiók, mielőtt aktívvá válik.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti Britta Simon számára az Azure egyszeri bejelentkezést a Jobscience-hez való hozzáférés biztosításával.

![Felhasználó hozzárendelése][200] 

**Ha Britta Simont hozzá szeretné rendelni a Feladattudományhoz, hajtsa végre a következő lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások nézetét, majd keresse meg a címtárnézetet, és nyissa meg a **Vállalati alkalmazások,** majd kattintson a **Minden alkalmazás**elemre .

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában válassza a **Feladattudomány**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. A bal oldali menüben kattintson a **Felhasználók és csoportok**parancsra.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson **a Hozzáadás** gombra. Ezután válassza a **Felhasználók és csoportok** lehetőséget a Hozzárendelés **hozzáadása** párbeszédpanelen.

    ![Felhasználó hozzárendelése][203]

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a Felhasználók listában.

1. Kattintson a **Kijelölés** gombra a **Felhasználók és csoportok** párbeszédpanelen.

1. Kattintson a **Hozzárendelés** **gombra** a Hozzárendelés hozzáadása párbeszédpanelen.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Feladattudományi csempére kattint, automatikusan bejelentkezik a Feladattudományi alkalmazásba.
A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](../user-help/active-directory-saas-access-panel-introduction.md)

## <a name="additional-resources"></a>További források

* [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

