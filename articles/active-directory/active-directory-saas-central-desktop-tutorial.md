---
title: "Oktatóanyag: Azure Active Directoryval integrált központi asztali |} Microsoft Docs"
description: "Megtudhatja, hogyan központi asztal használata az Azure Active Directoryval az egyszeri bejelentkezés, automatikus kiépítésének, és több!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: fe32c1d68040ceb9d9de2ad6c4a6dc9ea93f5aef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Oktatóanyag: Azure Active Directory-integráció központi asztal
Ez az oktatóanyag célja az integráció az Azure és a központi asztal megjelenítése. Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

* Egy érvényes Azure-előfizetés
* Egy központi asztali az egyszeri bejelentkezés engedélyezett előfizetés / központi asztali bérlői

Ebben az oktatóanyagban leírt forgatókönyv az alábbi építőelemeket áll:

* A központi asztali alkalmazás-integráció engedélyezése
* Egyszeri bejelentkezés (SSO) konfigurálása
* Felhasználók átadására
* Felhasználók hozzárendelése

![A forgatókönyv](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "forgatókönyv")

## <a name="enable-the-application-integration-for-central-desktop"></a>A központi asztali alkalmazás-integráció engedélyezése
Ez a szakasz célja helyzeteit vázolják fel, az alkalmazás központi asztal-integráció engedélyezése.

**Ahhoz, hogy az alkalmazás-integráció központi asztali, hajtsa végre a következő lépéseket:**

1. A klasszikus Azure portálon, a bal oldali navigációs panelen kattintson a **Active Directory**.
   
   ![Az Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
   ![Alkalmazások](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "alkalmazások")
4. Kattintson a **Hozzáadás** az oldal alján.
   
   ![Alkalmazás hozzáadása](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "alkalmazás hozzáadása")
5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.
   
   ![Alkalmazás hozzáadása a gallerry](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "gallerry az alkalmazás hozzáadása")
6. Az a **keresőmezőbe**, típus **központi asztali**.
   
   ![Alkalmazáskatalógusában](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "alkalmazáskatalógusában")
7. Az eredmények ablaktáblájában válassza **központi asztali**, és kattintson a **Complete** hozzáadása az alkalmazáshoz.
   
   ![Központi asztali](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "központi asztali")
   
## <a name="configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása

Ez a szakasz célja felvázoló engedélyezése a felhasználók a fiókjukkal központi asztalra hitelesítést az Azure AD összevonási alapján a SAML protokoll használatával.

Ez az eljárás részeként kötelesek base-64 kódolású tanúsítvány feltöltése a központi asztal-bérlő.  
Ha nem ismeri ezt az eljárást, tekintse meg a [bináris tanúsítvány szöveg fájlba való konvertálása](http://youtu.be/PlgrzUZ-Y1o).

**Egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon a a **központi asztali** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a ** konfigurálása egyszeri bejelentkezés ** párbeszédpanel.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "egyszeri bejelentkezés konfigurálása")
2. Az a **hová felhasználók bejelentkezhetnek a központi asztalra** lapon jelölje be **Microsoft Azure AD az egyszeri bejelentkezés**, és kattintson a **tovább**.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "egyszeri bejelentkezés konfigurálása")
3. Az a **alkalmazás URL-cím konfigurálása** lapon hajtsa végre az alábbi lépéseket, és kattintson a **következő**: 
   
   1. Az a **központi asztali bejelentkezési az URL-címet** szövegmező, a központi asztali bérlői URL-címét (pl.: *http://contoso.centraldesktop.com*).
   2. Központi asztali válasz URL-cím szövegmezőbe írja be a központi asztali AssertionConsumerService URL-címet (pl.: https://contoso.centraldesktop.com/saml2-assertion.php).
   
   >[!NOTE]
   >Az érték beszerezni a központi asztali metaadatok (pl.: *http://contoso.centraldesktop.com*).
   >  
   
   ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "alkalmazás URL-CÍMEK konfigurálása")
4. A a **konfigurálása egyszeri bejelentkezéshez központi asztali** töltse le a tanúsítványt, kattintson **tanúsítvánnyal letöltés**, majd mentse a tanúsítványfájlt, a számítógépen.
   
  ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "egyszeri bejelentkezés konfigurálása")
5. Jelentkezzen be a **központi asztali** bérlő.
6. Nyissa meg a **beállítások**, kattintson a **speciális**, és kattintson a **egyszeri bejelentkezés**.
   
  ![A telepítő - speciális](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "telepítő – speciális")
7. Az a **egyetlen bejelentkezést a beállítások** lapon, a következő lépésekkel:
   
  ![Egyszeri bejelentkezés beállításai](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "egyszeri bejelentkezés beállításai")
   
  1. Válassza ki **engedélyezése SAML v2 az egyszeri bejelentkezés**.
  2. A klasszikus Azure portálon a a **konfigurálása egyszeri bejelentkezéshez központi asztali** lapon, másolja a **kiállítójának URL-címe** értékét, és illessze be azt a **egyszeri bejelentkezési URL-cím** szövegmező.
  3. A klasszikus Azure portálon a a **konfigurálása egyszeri bejelentkezéshez központi asztali** lapon, másolja a **távoli bejelentkezési URL-cím** értékét, és illessze be azt a **Egyszeri bejelentkezési URL-cím** szövegmező .
  4. A klasszikus Azure portálon a a **konfigurálása egyszeri bejelentkezéshez központi asztali** lapon, másolja a **egyetlen Sign-Out URL-címe** értékét, és illessze be azt a **SSO kijelentkezési URL-cím**szövegmező.
8. Az a **üzenet aláírás-ellenőrzési módszer** területen tegye a következőket:
   
   ![Üzenet-aláírást ellenőrzési módszer](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "üzenet-aláírást ellenőrzési módszer")
   
  1. Válassza ki **tanúsítvány**.
  2. Az a **SSO tanúsítvány** listáról válassza ki **RSH SHA256**.
  3. Hozzon létre egy szövegfájlt a letöltött tanúsítvány, másolja a tartalmat a szöveges fájl, és illessze be azt a **SSO tanúsítvány** mező.  
     >[!TIP]
     >További részletekért lásd: [bináris tanúsítvány szöveg fájlba való konvertálása](http://youtu.be/PlgrzUZ-Y1o)
      >  
   4. Válassza ki **jeleníti meg a hivatkozást a SAMLv2 bejelentkezési**.
9. Kattintson a **frissítés**.
10. A klasszikus Azure portálon, válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **Complete** bezárásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "egyszeri bejelentkezés konfigurálása")
    
## <a name="configure-user-provisioning"></a>A felhasználók átadása konfigurálása

Az AAD-felhasználókat kell jelentkezhetnek be akkor ki kell építenie a központi asztali alkalmazások. Ez a szakasz ismerteti, hogyan AAD felhasználói fiókokat hozhat létre a központi asztali.

**Felhasználói fiókok központi asztalra telepítéséhez:**
1. Jelentkezzen be a központi asztali bérlő.
2. Ugrás a **személyek \> belső tagok**.
3. Kattintson a **belső tagok felvétele**.
   
  ![Személyek](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "személyek")
4. Az a **E-mail címet az új tagok** szövegmező, írja be egy AAD-fiókba rendelkezés szeretne, és kattintson a **következő**.
   
  ![E-mail-címeket az új tagok](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "E-mail címet az új tagok")
5. Kattintson a **hozzáadása belső tag**.
   
  ![Adja hozzá a belső tag](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "belső tag hozzáadása")
   
   >[!NOTE]
   >A hozzáadott felhasználók kattintson a fiók aktiválásához szükséges megerősítési hivatkozást tartalmazó e-mailt fog kapni.
   > 

>[!NOTE]
>Bármely más központi asztali felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz központi asztali által nyújtott API-k
>  

## <a name="assign-users"></a>Felhasználók hozzárendelése
A konfiguráció teszteléséhez kell biztosítania az Azure AD-felhasználók számára engedélyezni, használja az alkalmazás elérésére hozzárendelésével.

**Felhasználók hozzárendelése központi asztali, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon hozzon létre egy olyan fiókot.
2. Az a **központi asztali** alkalmazás integráció lapján, kattintson a **felhasználók hozzárendelése**.
   
   ![Felhasználók hozzárendelése](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "felhasználók hozzárendelése")
3. Adja meg a tesztfelhasználó számára, kattintson **hozzárendelése**, és kattintson a **Igen** a hozzárendelés megerősítéséhez.
   
   ![Igen](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Igen")

Ha azt szeretné, az egyszeri bejelentkezés beállításainak ellenőrzéséhez nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további tudnivalókért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

