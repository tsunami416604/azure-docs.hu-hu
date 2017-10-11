---
title: "Oktatóanyag: Azure Active Directoryval integrált Cisco Webex |} Microsoft Docs"
description: "Megtudhatja, hogyan Cisco Webex használata az Azure Active Directoryval az egyszeri bejelentkezés, automatizált üzembe helyezést és további engedélyezéséhez!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: b44b1a5b3e988a51db3325ec8a181651fa84e768
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Oktatóanyag: Azure Active Directoryval integrált Cisco Webex
Ez az oktatóanyag célja az Azure és a Cisco Webex integrálását megjelenítése.  
Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

* Egy érvényes Azure-előfizetés
* A Cisco Webex bérlő

Ez az oktatóanyag befejezése után a Cisco Webex rendelt Azure AD-felhasználók fog tudni egyetlen jelentkezzen be az alkalmazás a Cisco Webex vállalati hely (a szolgáltatás a szolgáltató által kezdeményezett bejelentkezési), vagy használja a [a hozzáférési Panel bemutatása ](active-directory-saas-access-panel-introduction.md).

Ebben az oktatóanyagban leírt forgatókönyv az alábbi építőelemeket áll:

* A Cisco Webex alkalmazás-integráció engedélyezése
* Egyszeri bejelentkezés (SSO) konfigurálása
* Felhasználók átadására
* Felhasználók hozzárendelése

![A forgatókönyv](./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "forgatókönyv")

## <a name="enable-the-application-integration-for-cisco-webex"></a>A Cisco Webex alkalmazás-integráció engedélyezése
Ez a szakasz célja helyzeteit vázolják fel, a Cisco Webex alkalmazás-integráció engedélyezése.

**Ahhoz, hogy az alkalmazás-integráció Cisco Webex, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon, a bal oldali navigációs panelen kattintson a **Active Directory**.
   
   ![Az Active Directory](./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
   ![Alkalmazások](./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "alkalmazások")
4. Kattintson a **Hozzáadás** az oldal alján.
   
   ![Alkalmazás hozzáadása](./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "alkalmazás hozzáadása")
5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.
   
   ![Alkalmazás hozzáadása a gallerry](./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "gallerry az alkalmazás hozzáadása")
6. Az a **keresőmezőbe**, típus **Cisco Webex**.
   
   ![Alkalmazáskatalógusában](./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Alkalmazáskatalógusában")
7. Az eredmények ablaktáblájában válassza **Cisco Webex**, és kattintson a **Complete** hozzáadása az alkalmazáshoz.
   
   ![Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
   
## <a name="configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása

Ez a szakasz célja felvázoló engedélyezése a felhasználók hitelesítéséhez Cisco Webex fiókkal az Azure AD összevonási alapján a SAML protokoll használatával.  

Ez az eljárás részeként kötelesek base-64 kódolású tanúsítvány létrehozása. Ha nem ismeri ezt az eljárást, tekintse meg a [bináris tanúsítvány szöveg fájlba való konvertálása](http://youtu.be/PlgrzUZ-Y1o)

**Egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon a a **Cisco Webex** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "egyszeri bejelentkezés konfigurálása")
2. Az a **hová felhasználók számára történő bejelentkezést Cisco Webex** lapon jelölje be **Microsoft Azure AD az egyszeri bejelentkezés**, és kattintson a **következő**.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "egyszeri bejelentkezés konfigurálása")
3. Az a **alkalmazás URL-cím konfigurálása** lapon hajtsa végre az alábbi lépéseket, és kattintson a **következő**.
   
   ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "alkalmazás URL-CÍMEK konfigurálása")   
   1. Az a **Sign URL-cím** szövegmező, írja be a Cisco Webex bérlői URL-cím (pl.: *http://contoso.webex.com*).
   2. Az a **Cisco Webex válasz URL-CÍMEN** szövegmezőhöz típusa a **Cisco Webex AssertionConsumerService URL-cím** (pl.: *https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company* ).
4. A a **konfigurálhatja az egyszeri bejelentkezés Cisco Webex** töltse le a tanúsítványt, kattintson **tanúsítvánnyal letöltés**, majd mentse a tanúsítványfájlt, a számítógépen.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "egyszeri bejelentkezés konfigurálása")
5. Egy másik webes böngészőablakban jelentkezzen be a Cisco Webex vállalati webhely rendszergazdaként.
6. Kattintson a felső menüben **helyfelügyelet**.
   
   ![Hely felügyelete](./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "hely felügyelete")
7. Az a **kezelése hely** területén kattintson **SSO konfigurációs**.
   
   ![Egyszeri bejelentkezés konfigurációs](./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "SSO-konfiguráció")
8. Az összevont webes egyszeri bejelentkezés konfigurálása a szakaszban a következő lépésekkel:
   
   ![Összevont egyszeri Bejelentkezéses konfigurációs](./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "összevont egyszeri bejelentkezés konfigurálása")  
   1. Az a **összevonási protokoll** listáról válassza ki **SAML 2.0**.
   2. Hozzon létre egy **Base-64 kódolású** fájlt a letöltött tanúsítvány.  
    >[!TIP]
    >További részletekért lásd: [bináris tanúsítvány szöveg fájlba való konvertálása](http://youtu.be/PlgrzUZ-Y1o)
    >  
   3. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben, és másolja a tartalmát.
   4. Kattintson a **SAML-metaadatok importálása**, majd illessze be a base-64 kódolású tanúsítvány.
   5. A klasszikus Azure portálon a a **konfigurálhatja az egyszeri bejelentkezés Cisco Webex** párbeszédpanel lap, a Másolás a **kiállítójának URL-címe** értékét, és illessze be azt a **SAML (IdP ID) kiállító** szövegmező.
   6. A klasszikus Azure portálon a a **konfigurálhatja az egyszeri bejelentkezés Cisco Webex** párbeszédpanel lap, a Másolás a **távoli bejelentkezési URL-cím** értékét, és illessze be azt a **ügyfél SSO szolgáltatás bejelentkezési URL-címe** szövegmező.
   7. Az a **NameID formátum** listáról válassza ki **E-mail cím**.
   8. Az a **AuthnContextClassRef** szövegmezőhöz típus **urn: oasis: nevek: tc: SAML:2.0:ac:classes:Password**.
   9. A klasszikus Azure portálon a a **konfigurálhatja az egyszeri bejelentkezés Cisco Webex** párbeszédpanel lap, a Másolás a **távoli kijelentkezési URL-cím** értékét, és illessze be azt a **ügyfél egyszeri bejelentkezési szolgáltatás kijelentkezési URL-címe** szövegmező.
   10. Kattintson a **frissítés**.
9. A klasszikus Azure portálon a a **konfigurálhatja az egyszeri bejelentkezés Cisco Webex** párbeszédpanel lapon válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **Complete**.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "egyszeri bejelentkezés konfigurálása")
   
## <a name="configure-user-provisioning"></a>A felhasználók átadása konfigurálása

Ahhoz, hogy az Azure AD-felhasználók Cisco Webex bejelentkezni, akkor ki kell építenie a Cisco Webex.  

* Cisco Webex, ha egy kézi tevékenység.

**A felhasználói fiókok létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Cisco Webex** bérlő.
2. Ugrás a **felhasználók kezelése \> felhasználó hozzáadása**.
   
   ![Felhasználók hozzáadása az](./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "felhasználók hozzáadása")
3. A felhasználó hozzáadása területen hajtsa végre a következő lépéseket:
   
   ![Felhasználó hozzáadása](./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "felhasználó hozzáadása")   
   1. Mint **fióktípus**, jelölje be **állomás**.
   2. Az adatokat egy meglévő Azure AD-felhasználó írja be a következő szövegmezők: **utónevét, vezetéknevét**, **felhasználónév**, **E-mail**, **jelszó**, **Jelszó megerősítése**.
   3. Kattintson az **Add** (Hozzáadás) parancsra.

>[!NOTE]
>Bármely más Cisco Webex felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz Cisco Webex által nyújtott API-k. 
> 

## <a name="assign-users"></a>Felhasználók hozzárendelése
A konfiguráció teszteléséhez kell biztosítania az Azure AD-felhasználók számára engedélyezni, használja az alkalmazás elérésére hozzárendelésével.

**Felhasználók hozzárendelése Cisco Webex, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon hozzon létre egy olyan fiókot.
2. Az a **Cisco Webex** alkalmazás integráció lapján, kattintson a **felhasználók hozzárendelése**.
   
   ![Felhasználók hozzárendelése](./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "felhasználók hozzárendelése")
3. Adja meg a tesztfelhasználó számára, kattintson **hozzárendelése**, és kattintson a **Igen** a hozzárendelés megerősítéséhez.
   
   ![Igen](./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Igen")

Ha azt szeretné, az egyszeri bejelentkezés beállításainak ellenőrzéséhez nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további tudnivalókért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

