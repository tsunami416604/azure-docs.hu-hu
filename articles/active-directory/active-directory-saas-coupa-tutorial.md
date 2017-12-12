---
title: "Oktatóanyag: Azure Active Directoryval integrált Coupa |} Microsoft Docs"
description: "Megtudhatja, hogyan Coupa használata az Azure Active Directoryval az egyszeri bejelentkezés, automatizált üzembe helyezést és további engedélyezéséhez!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: ae561324add7bf0a792ae688552b8e07853a5859
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Oktatóanyag: Azure Active Directoryval integrált Coupa
Ez az oktatóanyag célja az Azure és Coupa integrálását megjelenítése.  
Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

* Egy érvényes Azure-előfizetés
* Egy Coupa egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés

Ez az oktatóanyag befejezése után az Azure AD felhasználók Coupa rendelt tudják az alkalmazás használatával történő egyszeri bejelentkezéshez a [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

Ebben az oktatóanyagban leírt forgatókönyv az alábbi építőelemeket áll:

* Az alkalmazás Coupa-integráció engedélyezése
* Egyszeri bejelentkezés konfigurálása
* Felhasználók átadására
* Felhasználók hozzárendelése

![A forgatókönyv](./media/active-directory-saas-coupa-tutorial/IC791897.png "forgatókönyv")

## <a name="enable-the-application-integration-for-coupa"></a>Az alkalmazás Coupa-integráció engedélyezése
Ez a szakasz célja felvázoló Coupa az alkalmazás-integráció engedélyezése.

**Ahhoz, hogy az alkalmazás-integráció Coupa, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon, a bal oldali navigációs panelen kattintson a **Active Directory**.
   
   ![Az Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
   ![Alkalmazások](./media/active-directory-saas-coupa-tutorial/IC700994.png "alkalmazások")
4. Kattintson a **Hozzáadás** az oldal alján.
   
   ![Alkalmazás hozzáadása](./media/active-directory-saas-coupa-tutorial/IC749321.png "alkalmazás hozzáadása")
5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.
   
   ![Alkalmazás hozzáadása a gallerry](./media/active-directory-saas-coupa-tutorial/IC749322.png "gallerry az alkalmazás hozzáadása")
6. Az a **keresőmezőbe**, típus **Coupa**.
   
   ![Alkalmazáskatalógusában](./media/active-directory-saas-coupa-tutorial/IC791898.png "Alkalmazáskatalógusában")
7. Az eredmények ablaktáblájában válassza **Coupa**, és kattintson a **Complete** hozzáadása az alkalmazáshoz.
   
   ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
   
## <a name="configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása

Ez a szakasz célja felvázoló engedélyezése a felhasználók hitelesítéséhez Coupa fiókkal az Azure AD összevonási alapján a SAML protokoll használatával.  

Egyszeri bejelentkezés az Coupa konfigurálásával meg kell ujjlenyomat értéket beolvasni egy tanúsítványt. Ha nem ismeri ezt az eljárást, tekintse meg a [hogyan lehet lekérni egy tanúsítvány-ujjlenyomat értékének](http://youtu.be/YKQF266SAxI).

**Egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Coupa vállalati webhely.
2. Ugrás a **telepítő \> biztonsági ellenőrzést**.
   
   ![Biztonsági vezérlők](./media/active-directory-saas-coupa-tutorial/IC791900.png "biztonsági vezérlőket")
3. A számítógépre a Coupa metaadatait tartalmazó fájl letöltéséhez kattintson **letöltési és SP metaadatok importálása**.
   
   ![Coupa SP metaadatok](./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa SP metaadatok")
4. Egy másik böngészőablakban jelentkezzen be a klasszikus Azure portálon.
5. Az a **Coupa** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-coupa-tutorial/IC791902.png "egyszeri bejelentkezés konfigurálása")
6. Az a **hová bejelentkezni Coupa felhasználók** lapon jelölje be **Microsoft Azure AD az egyszeri bejelentkezés**, és kattintson a **következő**.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-coupa-tutorial/IC791903.png "egyszeri bejelentkezés konfigurálása")
7. Az a **alkalmazás URL-cím konfigurálása** lapon, a következő lépésekkel:
   
   ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-coupa-tutorial/IC791904.png "alkalmazás URL-CÍMEK konfigurálása")   
   1. Az a **URL-cím bejelentkezési** szövegmező, jelentkezzen be a Coupa alkalmazás a felhasználók által használt típus URL-cím (pl.: "*http://company.Coupa.com*").
   2. Nyissa meg a letöltött Coupa metaadat-fájlt, és másolja a **AssertionConsumerService index/URL-cím**.
   3. Az a **Coupa válasz URL-CÍMEN** szövegmező, illessze be a **AssertionConsumerService index/URL-cím** érték.
   4. Kattintson a **Tovább** gombra.
8. A a **konfigurálhatja az egyszeri bejelentkezés Coupa** töltse le a metaadat-fájlt, kattintson **metaadatok letöltése**, és mentse helyileg a fájlt a számítógépen.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-coupa-tutorial/IC791905.png "egyszeri bejelentkezés konfigurálása")
9. Lépjen a Coupa vállalati helyen **telepítő \> biztonsági ellenőrzést**.
   
   ![Biztonsági vezérlők](./media/active-directory-saas-coupa-tutorial/IC791900.png "biztonsági vezérlőket")
10. Az a **jelentkezzen be Coupa hitelesítő adatok használatával** területen tegye a következőket:  

   ![Jelentkezzen be Coupa hitelesítő adatok használatával](./media/active-directory-saas-coupa-tutorial/IC791906.png "jelentkezzen be Coupa hitelesítő adatok használatával") 
   1. Válassza ki **jelentkezzen be SAML**.
   2. Kattintson a **Tallózás** fel kell töltenie a letöltött Azure Active metaadat-fájlt.
   3. Kattintson a **Save** (Mentés) gombra.
11. A klasszikus Azure portálon, válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **Complete** bezárásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
    
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-coupa-tutorial/IC791907.png "egyszeri bejelentkezés konfigurálása")
    
## <a name="configure-user-provisioning"></a>A felhasználók átadása konfigurálása

Ahhoz, hogy az Azure AD-felhasználók Coupa bejelentkezni, akkor ki kell építenie Coupa be.  

* Coupa, ha egy kézi tevékenység.

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Coupa** vállalati hely rendszergazdaként.
2. Kattintson a felső menüben **telepítő**, és kattintson a **felhasználók**.
   
   ![Felhasználók](./media/active-directory-saas-coupa-tutorial/IC791908.png "felhasználók")
3. Kattintson a **Create** (Létrehozás) gombra.
   
   ![Felhasználók létrehozása](./media/active-directory-saas-coupa-tutorial/IC791909.png "felhasználók létrehozása")
4. Az a **felhasználó hozhat létre** területen tegye a következőket:
   
   ![Felhasználó adatait](./media/active-directory-saas-coupa-tutorial/IC791910.png "felhasználó részletei")
   
   1. Típus a **bejelentkezési**, **Utónév**, **Vezetéknév**, **egyszeri bejelentkezési azonosító**, **E-mail** egy érvényes Azure Active Directory-fiókot szeretné azokat a kapcsolódó szövegmezők rendelkezés attribútumait.
   2. Kattintson a **Create** (Létrehozás) gombra.   
   >[!NOTE]
   >Az Azure Active Directory fióktulajdonos kap egy e-mailt egy hivatkozás, mielőtt aktívvá válik, győződjön meg arról, hogy a fiók. 
   > 

>[!NOTE]
>Bármely más Coupa felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz Coupa által nyújtott API-k. 
> 

## <a name="assign-users"></a>Felhasználók hozzárendelése
A konfiguráció teszteléséhez kell biztosítania az Azure AD-felhasználók számára engedélyezni, használja az alkalmazás elérésére hozzárendelésével.

**Felhasználók hozzárendelése Coupa, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon hozzon létre egy olyan fiókot.
2. Az a ** Coupa ** alkalmazás integráció lapján, kattintson a **felhasználók hozzárendelése**.
   
   ![Felhasználók hozzárendelése](./media/active-directory-saas-coupa-tutorial/IC791911.png "felhasználók hozzárendelése")
3. Adja meg a tesztfelhasználó számára, kattintson **hozzárendelése**, és kattintson a **Igen** a hozzárendelés megerősítéséhez.
   
   ![Igen](./media/active-directory-saas-coupa-tutorial/IC767830.png "Igen")

Ha azt szeretné, az egyszeri bejelentkezés beállításainak ellenőrzéséhez nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további tudnivalókért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

