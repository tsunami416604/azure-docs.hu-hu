---
title: "Oktatóanyag: Azure Active Directoryval integrált TOPdesk - biztonságos |} Microsoft Docs"
description: "TOPdesk - használata az Azure Active Directoryval engedélyezése egyszeri bejelentkezéshez, automatizált üzembe helyezést és további biztonságos!."
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: 8e149d2d-7849-48ec-9993-31f4ade5fdb4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: e165a18c5a24071d52838ca95edbb5e6b39e63a5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Oktatóanyag: Azure Active Directoryval integrált TOPdesk - biztonságos
Ez az oktatóanyag célja az Azure és TOPdesk - biztonságos integrálását megjelenítése.  
Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

* Egy érvényes Azure-előfizetés
* A TOPdesk - biztonságos egyszeri bejelentkezés engedélyezve van az előfizetés

Ez az oktatóanyag befejezése után az Azure AD-felhasználók TOPdesk - biztonságos rendelt fog tudni egyetlen jelentkezzen be az alkalmazást a TOPdesk - biztonságos vállalati helyhez (szolgáltatás szolgáltató által kezdeményezett bejelentkezés), vagy a [a hozzáférés bemutatása Panel](active-directory-saas-access-panel-introduction.md).

Ebben az oktatóanyagban leírt forgatókönyv az alábbi építőelemeket áll:

1. A TOPdesk - biztonságos alkalmazás-integráció engedélyezése
2. Egyszeri bejelentkezés konfigurálása
3. Felhasználók átadására
4. Felhasználók hozzárendelése

![A forgatókönyv](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "forgatókönyv")

## <a name="enabling-the-application-integration-for-topdesk---secure"></a>A TOPdesk - biztonságos alkalmazás-integráció engedélyezése
Ez a szakasz célja a TOPdesk - biztonságos alkalmazás-integráció engedélyezése felvázoló.

### <a name="to-enable-the-application-integration-for-topdesk---secure-perform-the-following-steps"></a>Ahhoz, hogy az alkalmazás-integráció TOPdesk - biztonságos, hajtsa végre a következő lépéseket:
1. A klasszikus Azure portálon, a bal oldali navigációs panelen kattintson a **Active Directory**.
   
    ![Az Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.

3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
    ![Alkalmazások](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "alkalmazások")

4. Kattintson a **Hozzáadás** az oldal alján.
   
    ![Alkalmazás hozzáadása](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "alkalmazás hozzáadása")

5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.
   
    ![Alkalmazás hozzáadása a gallerry](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "gallerry az alkalmazás hozzáadása")

6. Az a **keresőmezőbe**, típus **TOPdesk - biztonságos**.
   
    ![Alkalmazáskatalógusában](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Alkalmazáskatalógusában")

7. Az eredmények ablaktáblájában válassza **TOPdesk - biztonságos**, és kattintson a **Complete** hozzáadása az alkalmazáshoz.
   
    ![TOPdesk - biztonságos](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - biztonságos")

## <a name="configuring-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása
Ez a szakasz célja engedélyezése a felhasználók számára a hitelesítést a TOPdesk - vázlat biztonságos fiókkal az Azure AD összevonási alapján a SAML protokoll használatával.  
Konfigurálása egyszeri bejelentkezéshez az TOPdesk - biztonságos kell embléma ikon fájl feltöltése. Ahhoz, hogy az ikonfájl, lépjen kapcsolatba a TOPdesk támogatási csapatával.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:
1. Jelentkezzen be a **TOPdesk - biztonságos** vállalati hely rendszergazdaként.
2. Az a **TOPdesk** menüben kattintson a **beállítások**.
   
    ![Beállítások](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "beállítások")

3. Kattintson a **bejelentkezési beállítások**.
   
    ![Bejelentkezési beállítások](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "bejelentkezési beállítások")

4. Bontsa ki a **bejelentkezési beállítások** menüben, majd kattintson **általános**.
   
    ![Általános](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "általános")

5. Az a **biztonságos** szakasza a **SAML bejelentkezési** konfigurációs szakaszban, hajtsa végre a következő lépéseket:
   
    ![Műszaki beállítások](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "műszaki beállításai")
   
    a. Kattintson a **letöltése** a nyilvános metaadatait tartalmazó fájl letöltéséhez, és mentse helyileg a számítógépen.
   
    b. Nyissa meg a metaadat-fájlt, és keresse meg a **AssertionConsumerService** csomópont.
    
    ![Helyességi feltétel fogyasztói szolgáltatás](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "helyességi feltétel fogyasztói szolgáltatás")
   
    c. Másolás a **AssertionConsumerService** érték.  
      
    > [!NOTE]
    > Szüksége lesz az értéket a **alkalmazás URL-cím konfigurálása** szakasz az oktatóanyag későbbi részében.
    > 
    > 

6. Egy másik webes böngészőablakban, jelentkezzen be a **a klasszikus Azure portálon** rendszergazdaként.

7. Az a **TOPdesk - biztonságos** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a ** konfigurálása egyszeri bejelentkezés ** párbeszédpanel.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "egyszeri bejelentkezés konfigurálása")

8. Az a **hová bejelentkezni TOPdesk - biztonságos felhasználók** lapon jelölje be **Microsoft Azure AD az egyszeri bejelentkezés**, és kattintson a **tovább**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "egyszeri bejelentkezés konfigurálása")

9. Az a **alkalmazás URL-cím konfigurálása** lapon, a következő lépésekkel:
   
    ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "alkalmazás URL-CÍMEK konfigurálása")
   
    a. Az a **TOPdesk - bejelentkezési biztonságos az URL-cím** szövegmező, írja be az URL-cím segítségével a felhasználók jelentkezzen be a TOPdesk - védett alkalmazás (pl.: "*https://qssolutions.topdesk.net*").
   
    b. Az a **TOPdesk – válasz URL-címe** szövegmező, illessze be a **TOPdesk - biztonságos AssertionConsumerService URL-** (pl.: "*https://qssolutions.topdesk.net/tas/public/login/saml*")
   
    c. Kattintson a **Tovább** gombra.

10. A a **konfigurálhatja az egyszeri bejelentkezés TOPdesk - biztonságos** töltse le a metaadat-fájlt, kattintson **metaadatok letöltése**, és mentse helyileg a fájlt a számítógépen.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "egyszeri bejelentkezés konfigurálása")

11. A tanúsítványfájl létrehozásához hajtsa végre az alábbi lépéseket:
    
    ![Tanúsítvány](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "tanúsítvány")
    
    a. Nyissa meg a letöltött metaadatait tartalmazó fájl.
    b. Bontsa ki a **RoleDescriptor** csomópont egy **xsi: type** a **táplált: ApplicationServiceType**.
    c. Másolja a értékének a **x.509** csomópont.
    d. Mentse a másolt **x.509** érték helyileg a fájlt a számítógépen.

12. A TOPdesk - a vállalati webhely biztonságos a **TOPdesk** menüben kattintson a **beállítások**.
    
    ![Beállítások](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "beállítások")

13. Kattintson a **bejelentkezési beállítások**.
    
    ![Bejelentkezési beállítások](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "bejelentkezési beállítások")

14. Bontsa ki a **bejelentkezési beállítások** menüben, majd kattintson **általános**.
    
    ![Általános](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "általános")

15. Az a **nyilvános** kattintson **Hozzáadás**.
    
    ![Adja hozzá](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "hozzáadása")

16. Az a **SAML-alapú konfigurációs Segéd** párbeszédpanel lapon, a következő lépésekkel:
    
    ![SAML-alapú konfigurációs Segéd](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "SAML-alapú konfigurációs Segéd")
    
    a. A letöltött metaadatait tartalmazó fájl feltöltése a **összevonási metaadatok**, kattintson a **Tallózás**.

    b. Töltse fel a tanúsítványfájlt, a **tanúsítvány (RSA)**, kattintson a **Tallózás**.

    c. Fel kell töltenie az embléma fájlt során kapott azonosítóértékeket TOPdesk terméktámogatással a **embléma ikon**, kattintson a **Tallózás**.

    d. Az a **felhasználói név attribútum** szövegmezőhöz típus **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    e. Az a **megjelenített név** szövegmező, adja meg a konfiguráció nevét.

    f. Kattintson a **Save** (Mentés) gombra.

17. A klasszikus Azure portálon, válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **Complete** bezárásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "egyszeri bejelentkezés konfigurálása")

## <a name="configuring-user-provisioning"></a>Felhasználók átadására
Ahhoz, hogy az Azure AD-felhasználók TOPdesk - be tudjon jelentkezni biztonságos, akkor ki kell építenie TOPdesk - biztonságos be.  
TOPdesk - esetén biztonságos, egy kézi tevékenység.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:
1. Jelentkezzen be a **TOPdesk - biztonságos** vállalati hely rendszergazdaként.
2. Kattintson a felső menüben **TOPdesk \> új \> támogatófájljait \> operátor**.
   
    ![Operátor](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "operátor")

3. Az a **új operátor** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Új operátor](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "új operátor")
   
    a. Kattintson az Általános fülre.
   
    b. Az a **vezetékneve** a szövegmező a **általános** területen írja be egy érvényes Azure Active Directory-fiókot rendelkezés kívánt utolsó nevét.
   
    c. Válassza ki a **hely** a fiók a **hely** szakasz.
   
    d. Az a **bejelentkezési név** a szövegmező a **TOPdesk bejelentkezési** területen írja be a felhasználó bejelentkezési nevét.
   
    e. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Bármely más TOPdesk - biztonságos felhasználói fiók létrehozása eszközök vagy TOPdesk - felhasználói fiókok AAD kiépítése biztonságos által nyújtott API-kat is használhatja.
> 
> 

## <a name="assigning-users"></a>Felhasználók hozzárendelése
A konfiguráció teszteléséhez kell biztosítania az Azure AD-felhasználók számára engedélyezni, használja az alkalmazás elérésére hozzárendelésével.

### <a name="to-assign-users-to-topdesk---secure-perform-the-following-steps"></a>Felhasználók hozzárendelése TOPdesk - biztonságos, hajtsa végre a következő lépéseket:
1. A klasszikus Azure portálon hozzon létre egy olyan fiókot.
2. Az a ** TOPdesk - biztonságos ** alkalmazás integráció lapján, kattintson a **felhasználók hozzárendelése**.
   
    ![Felhasználók hozzárendelése](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "felhasználók hozzárendelése")

3. Adja meg a tesztfelhasználó számára, kattintson **hozzárendelése**, és kattintson a **Igen** a hozzárendelés megerősítéséhez.
   
    ![Igen](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Igen")

Ha azt szeretné, az egyszeri bejelentkezés beállításainak ellenőrzéséhez nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további tudnivalókért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

