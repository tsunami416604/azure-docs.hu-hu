---
title: "Oktatóanyag: Azure Active Directoryval integrált Replicon |} Microsoft Docs"
description: "Megtudhatja, hogyan Replicon használata az Azure Active Directoryval az egyszeri bejelentkezés, automatizált üzembe helyezést és további engedélyezéséhez!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
ms.openlocfilehash: 2d735ceb8be2a3ff5bef33b1c39969591afca3dc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Oktatóanyag: Azure Active Directoryval integrált Replicon
Ez az oktatóanyag célja az Azure és Replicon integrálását megjelenítése. Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

* Egy érvényes Azure-előfizetés
* A Replicon bérlő

Ez az oktatóanyag befejezése után az Azure AD-felhasználók Replicon rendelt fog tudni egyetlen jelentkezzen be az alkalmazás a Replicon vállalati hely (a szolgáltatás a szolgáltató által kezdeményezett bejelentkezési), vagy használja a [a hozzáférési Panelbemutatása](active-directory-saas-access-panel-introduction.md).

Ebben az oktatóanyagban leírt forgatókönyv az alábbi építőelemeket áll:

1. Az alkalmazás Replicon-integráció engedélyezése
2. Egyszeri bejelentkezés (SSO) konfigurálása
3. Felhasználók átadására
4. Felhasználók hozzárendelése

![A forgatókönyv](./media/active-directory-saas-replicon-tutorial/IC777798.png "forgatókönyv")

## <a name="enable-the-application-integration-for-replicon"></a>Az alkalmazás Replicon-integráció engedélyezése
Ez a szakasz célja felvázoló Replicon az alkalmazás-integráció engedélyezése.

**Ahhoz, hogy az alkalmazás-integráció Replicon, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon, a bal oldali navigációs panelen kattintson a **Active Directory**.
   
    ![Az Active Directory](./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
    ![Alkalmazások](./media/active-directory-saas-replicon-tutorial/IC700994.png "alkalmazások")
4. Kattintson a **Hozzáadás** az oldal alján.
   
    ![Alkalmazás hozzáadása](./media/active-directory-saas-replicon-tutorial/IC749321.png "alkalmazás hozzáadása")
5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.
   
    ![Alkalmazás hozzáadása a gallerry](./media/active-directory-saas-replicon-tutorial/IC749322.png "gallerry az alkalmazás hozzáadása")
6. Az a **keresőmezőbe**, típus **Replicon**.
   
    ![Alkalmazáskatalógusában](./media/active-directory-saas-replicon-tutorial/IC777799.png "alkalmazáskatalógusában")
7. Az eredmények ablaktáblájában válassza **Replicon**, és kattintson a **Complete** hozzáadása az alkalmazáshoz.
   
    ![Replicon](./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
   
## <a name="configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása

Ez a szakasz célja felvázoló engedélyezése a felhasználók hitelesítéséhez Replicon fiókkal az Azure AD összevonási alapján a SAML protokoll használatával.

**Egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon a a **Replicon** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-replicon-tutorial/IC777801.png "egyszeri bejelentkezés konfigurálása")
2. Az a **hová bejelentkezni Replicon felhasználók** lapon jelölje be **Microsoft Azure AD az egyszeri bejelentkezés**, és kattintson a **következő**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-replicon-tutorial/IC777802.png "egyszeri bejelentkezés konfigurálása")
3. Az a **alkalmazás URL-cím konfigurálása** lapon, a következő lépésekkel:
   
    ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-replicon-tutorial/IC777803.png "alkalmazás URL-CÍMEK konfigurálása")
  1. Az a **Replicon bejelentkezési URL-cím** szövegmező, írja be a Replicon bérlői URL-cím (pl.: *https://na2.replicon.com/company/saml2/sp-sso/post*).
  2. Az a **Replicon válasz URL-CÍMEN** szövegmező, írja be a Replicon **AssertionConsumerService** URL-cím (pl.: *https://global.replicon.com/! / post egy saml2/vállalati/sso*).  
      
     >[!NOTE]
     >Az URL-cím beszerzése lévő Replicon metaadatok: **https://global.replicon.com/! /saml2/\<YourCompanyKey\>**.
     > 
     > 
 
  3. Kattintson a **Tovább** gombra.

4. A a **konfigurálhatja az egyszeri bejelentkezés Replicon** töltse le a metaadatokat, kattintson **metaadatok letöltése**, és mentse a metaadatok a számítógépen.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-replicon-tutorial/IC777804.png "egyszeri bejelentkezés konfigurálása")
5. Egy másik webes böngészőablakban jelentkezzen be a Replicon vállalati webhely rendszergazdaként.

6. SAML 2.0 konfigurálásához hajtsa végre az alábbi lépéseket:
   
    ![SAML-alapú hitelesítés engedélyezéséhez](./media/active-directory-saas-replicon-tutorial/IC777805.png "engedélyezése SAML-alapú hitelesítés")
  
  1. A megjelenítendő a **EnableSAML Authentication2** párbeszédpanel, az URL-cím, a következő bővítése után a vállalat kulcs: **/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**  
    * Az alábbiakban látható a teljes URL-cím sémája:  
   **https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**
   2. Kattintson a  **+**  bontsa ki a **v20Configuration** szakasz.
   3. Kattintson a  **+**  bontsa ki a **metaDataConfiguration** szakasz.
   4. Kattintson a **Choose File**ki az identity provider metaadatok XML-fájlt, majd kattintson a **Submit**.

7. A klasszikus Azure portálon, válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **Complete** bezárásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-replicon-tutorial/IC778418.png "egyszeri bejelentkezés konfigurálása")
   
## <a name="configure-user-provisioning"></a>A felhasználók átadása konfigurálása

Ahhoz, hogy az Azure AD-felhasználók Replicon bejelentkezni, akkor ki kell építenie Replicon be.  

Replicon, ha egy kézi tevékenység.

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. A webböngésző ablakának jelentkezzen be a Replicon vállalati webhely rendszergazdaként.
2. Ugrás a **felügyeleti \> felhasználók**.
   
    ![Felhasználók](./media/active-directory-saas-replicon-tutorial/IC777806.png "felhasználók")
3. Kattintson a **hozzáadni a felhasználót +**.
   
    ![Felhasználó hozzáadása](./media/active-directory-saas-replicon-tutorial/IC777807.png "felhasználó hozzáadása")
4. Az a **felhasználói profil** területen tegye a következőket:
   
    ![Felhasználói profil](./media/active-directory-saas-replicon-tutorial/IC777808.png "felhasználói profil")
   
  1. Az a **bejelentkezési név** szövegmezőhöz rendelkezés kívánt Azure AD-felhasználó e-mail címe típus az Azure AD.
  2. Mint **hitelesítési típus**, jelölje be **SSO**.
  3. Az a **részleg** szövegmező, írja be a felhasználói osztály.
  4. Mint **alkalmazott típus**, jelölje be **rendszergazda**.
  5. Kattintson a **felhasználói profil mentése**.

>[!NOTE]
>Bármely más Replicon felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz Replicon által nyújtott API-k.
> 
> 

## <a name="assign-users"></a>Felhasználók hozzárendelése
A konfiguráció teszteléséhez kell biztosítania az Azure AD-felhasználók számára engedélyezni, használja az alkalmazás elérésére hozzárendelésével.

**Felhasználók hozzárendelése Replicon, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon hozzon létre egy olyan fiókot.

2. Az a **Replicon** alkalmazás integráció lapján, kattintson a **felhasználók hozzárendelése**.
   
    ![Felhasználók hozzárendelése](./media/active-directory-saas-replicon-tutorial/IC777809.png "felhasználók hozzárendelése")

3. Adja meg a tesztfelhasználó számára, kattintson **hozzárendelése**, és kattintson a **Igen** a hozzárendelés megerősítéséhez.
   
    ![Igen](./media/active-directory-saas-replicon-tutorial/IC767830.png "Igen")

Ha azt szeretné, az egyszeri bejelentkezés beállításainak ellenőrzéséhez nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további tudnivalókért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

