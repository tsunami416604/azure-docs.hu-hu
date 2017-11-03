---
title: "Oktatóanyag: Azure Active Directoryval integrált Salesforce védőfal |} Microsoft Docs"
description: "Útmutató a Salesforce védőfal használata az Azure Active Directoryval az egyszeri bejelentkezés, automatikus üzembe helyezést, és több engedélyezéséhez!."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 32835e79188806bb2ff319eea23b1b52ab585ab1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Oktatóanyag: Azure Active Directoryval integrált Salesforce védőfal

Ez az oktatóanyag célja az Azure és a Salesforce védőfal integrálását megjelenítése.  

>[!TIP]
>Visszajelzés, tekintse meg a [az Azure támogatási lap](http://go.microsoft.com/fwlink/?LinkId=521878). 
> 

Védőfalak biztosítanak a szervezet több példánya létre külön környezetekben a számos célra, például a fejlesztői, tesztelési, és a képzési, az adatok és alkalmazások Salesforce éles vállalati veszélyeztetése nélkül.  

További részletekért lásd: [védőfal – áttekintés](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

* Egy érvényes Azure-előfizetés
* A védőfal a Salesforce.com-on

Ha egy érvényes védőfal még nem rendelkezik a Salesforce.com-on, akkor lépjen kapcsolatba a Salesforce.

Ebben az oktatóanyagban leírt forgatókönyv az alábbi építőelemeket áll:

1. A Salesforce védőfal alkalmazás-integráció engedélyezése
2. Egyszeri bejelentkezés (SSO) konfigurálása
3. A tartomány engedélyezése
4. Felhasználók átadására
5. Felhasználók hozzárendelése

![A forgatókönyv](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "forgatókönyv")

## <a name="enable-the-application-integration-for-salesforce-sandbox"></a>A Salesforce védőfal alkalmazás-integráció engedélyezése
Ez a szakasz célja felvázoló Salesforce védőfal az alkalmazás-integráció engedélyezése.

**Ahhoz, hogy az alkalmazás integráció Salesforce védőfal, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon, a bal oldali navigációs panelen kattintson a **Active Directory**.
   
   ![Az Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
   ![Alkalmazások](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "alkalmazások")
4. Lehetőségre a **Alkalmazáskatalógusában**, kattintson a **alkalmazás hozzáadása**, és kattintson a **hozzáadhat egy alkalmazást a saját szervezet által használható**.
   
   ![Választható? ] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "Mi történjen a teendő?")
5. Az a **keresőmezőbe**, típus **Salesforce védőfal**.
   
   ![Alkalmazáskatalógusában](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Alkalmazáskatalógusában")
6. Az eredmények ablaktáblájában válassza **Salesforce védőfal**, és kattintson a **Complete** hozzáadása az alkalmazáshoz.
   
   ![Salesforce védőfal](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce védőfal")
   
## <a name="configur-single-sign-on-sso"></a>Configur egyszeri bejelentkezés (SSO)

Ez a szakasz célja felvázoló engedélyezése a felhasználók hitelesítéséhez Salesforce fiókkal az Azure AD összevonási alapján a SAML protokoll használatával.

**Egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon a a **Salesforce védőfal** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "egyszeri bejelentkezés konfigurálása")
2. A a **hová bejelentkezni Salesforce védőfal felhasználók** lapon jelölje be **Microsoft Azure AD az egyszeri bejelentkezés**, és kattintson a **következő**.
   
   ![Salesforce védőfal](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce védőfal")
3. Az a **alkalmazás URL-cím konfigurálása** lap a **URL-cím bejelentkezési** szövegmező, írja be az URL-CÍMÉT a következő mintát `http://company.my.salesforce.com`, és kattintson a **tovább**.
   
   ![Alkalmazás URL-CÍMEK konfigurálása](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "alkalmazás URL-CÍMEK konfigurálása")
4. Ha már beállította egyszeri bejelentkezés Salesforce védőfal egy másik példány a könyvtárban, majd konfigurálnia kell a **azonosító** ugyanazt az értéket, hogy a **bejelentkezési URL-cím**. 
 * A **azonosító** mező található ellenőrzésével a **megjelenítése speciális beállítások** jelölőnégyzetet a **alkalmazás URL-cím konfigurálása** párbeszédpanel oldalán.
5. A a **konfigurálhatja az egyszeri bejelentkezés Salesforce védőfal** lapján kattintson **tanúsítvánnyal letöltés**, majd mentse a tanúsítványfájlt, a számítógépen.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "egyszeri bejelentkezés konfigurálása")
6. Egy másik webes böngészőablakban jelentkezzen be a Salesforce védőfal rendszergazdaként.
7. Kattintson a felső menüben **telepítő**.
   
   ![A telepítő](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "beállítása")
8. A bal oldali navigációs ablaktábláján kattintson **biztonsági vezérlők**, és kattintson a **egyszeri bejelentkezési beállítások**.
   
   ![Az egyszeri bejelentkezés beállítások](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "az egyszeri bejelentkezés beállításai")
9. Egyszeri bejelentkezés beállítások csoportjában hajtsa végre a következő lépéseket:
   
   ![Az egyszeri bejelentkezés beállítások](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "az egyszeri bejelentkezés beállításai")  
 1.  Válassza ki **SAML engedélyezett**. 
 2.  Kattintson az **Új** lehetőségre.
10. A SAML egyszeri bejelentkezés beállítások szakaszban hajtsa végre a következő lépéseket:
    
    ![SAML-alapú egyszeri bejelentkezés beállítások](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "SAML-alapú egyszeri bejelentkezési beállítások")  
 1. A szövegmezőben, írja be a konfiguráció nevét (pl.: *SPSSOWAAD\_teszt*). 
 2. A klasszikus Azure portálon a a **konfigurálhatja az egyszeri bejelentkezés Salesforce védőfal** párbeszéd lap, a Másolás a **kiállítójának URL-címe** értékét, és illessze be azt a **kibocsátó** szövegmező.
 3. Az a **entitásazonosító** szövegmezőhöz típus **https://test.salesforce.com** Ha ez az első Salesforce védőfal példány, hogy a címtárban ad hozzá. Ha már van egy példánya Salesforce védőfal, majd a a **Entitásazonosító** írja be a **URL-cím bejelentkezési**, amely a következő formátumban kell lennie:`http://company.my.salesforce.com`   
 4. Kattintson a **Tallózás** a letöltött tanúsítvány feltöltése.  
 5. Mint **SAML identitástípus**, jelölje be **helyességi feltételt tartalmaz az összevonási azonosító felhasználó**. 
 6. Mint **SAML-alapú identitás hely**, jelölje be **identitás a tulajdonos utasítás NameIdentifier elemében van**.
 7. A klasszikus Azure portálon a a **konfigurálhatja az egyszeri bejelentkezés Salesforce védőfal** párbeszéd lap, a Másolás a **távoli bejelentkezési URL-cím** értékét, és illessze be azt a **Identity Provider bejelentkezési URL-cím** szövegmező. 
 8. SFDC nem támogatja az SAML jelentkezzen ki.  A probléma megoldásához, illessze be a "https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0" be azt a **Identity Provider kijelentkezési URL-cím** szövegmező.
 9. Mint **szolgáltató által kezdeményezett kérelem Szolgáltatáskötés**, jelölje be **HTTP POST**. 
 10. Kattintson a **Save** (Mentés) gombra.
11. A klasszikus Azure portálon, válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **Complete** bezárásához a **konfigurálása egyszeri bejelentkezés** párbeszédpanel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "egyszeri bejelentkezés konfigurálása")

## <a name="enable-your-domain"></a>A tartomány
Jelen szakaszban feltételezzük, hogy már létrehozta a tartományhoz.  További részletekért lásd: [meghatározása saját tartomány neve](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Ahhoz, hogy a tartomány, hajtsa végre a következő lépéseket:**

1. A bal oldali navigációs ablaktáblán kattintson **tartományok**, és kattintson a **saját tartomány.**
   
   ![Saját tartomány](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "saját tartomány")
   
   >[!NOTE]
   >Győződjön meg arról, hogy a tartomány megfelelően van konfigurálva. 
   > 
2. Az a **bejelentkezési oldal beállításainak** területen kattintson **szerkesztése**, majd, mint **hitelesítési szolgáltatás**, válassza ki a nevét a SAML egyszeri bejelentkezés beállítása a fenti szakaszban leírt, és végül kattintson **mentése**.
   
   ![Saját tartomány](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "saját tartomány")

Amint egy tartományhoz, konfigurálva van, a felhasználók használjon bejelentkezni a Salesforce védőfal tartomány URL-CÍMÉT.  

Ahhoz, hogy az URL-cím értékét, kattintson az előző szakaszban létrehozott SSO profilra.

## <a name="configure-user-provisioning"></a>A felhasználók átadása konfigurálása
Ez a szakasz célja felvázoló engedélyezése a felhasználók átadása, az Active Directory felhasználói fiókoknak Salesforce védőfal felé.

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. A Salesforce-portálon, a felső navigációs sávon válassza ki a nevét, bontsa ki a felhasználó menüben:
   
   ![A beállítások](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "beállítások")
2. A felhasználó menüből válassza ki a **saját beállítások** megnyitásához a **saját beállítások** lap.
3. Kattintson a bal oldali ablaktáblában **személyes** bontsa ki a személyes szakaszt, és kattintson **alaphelyzetbe állítani a biztonsági jogkivonat**:
   
   ![A beállítások](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "beállítások")
4. A a **alaphelyzetbe állítani a biztonsági jogkivonat** kattintson **alaphelyzetbe állítani a biztonsági jogkivonat** kérjen a Salesforce.com biztonsági jogkivonatot tartalmazó e-maileket.
   
   ![Új jogkivonat](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "új jogkivonat")
5. Ellenőrizze a Salesforce.com az e-mailt a beérkezett e-mail "**esetbejegyzéseinek biztonsági megerősítő**" tulajdonos szerint.
6. Tekintse át az e-mailt, és másolja a biztonsági token értékét.
7. A klasszikus Azure portálon a a **salesforce védőfal** alkalmazás integráció lapján, kattintson a **konfigurálja, a felhasználók átadása** megnyitásához a **konfigurálhatja a felhasználók átadása** párbeszédpanel.
   
   ![Konfigurálja a felhasználók átadása](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "felhasználólétesítés konfigurálása")
8. Az a **hitelesítő adatait ahhoz, hogy a felhasználók automatikus átadása Salesforce védőfal** lapján adja meg a következő konfigurációs beállításokat:
   
   ![Salesforce védőfal](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce védőfal")   
 1. Az a **Salesforce védőfal rendszergazda felhasználóneve** szövegmezőhöz Salesforce védőfalat a fióknevet, amelynek típusa a **rendszergazda** Salesforce.com rendelt profillal.
 2. Az a **Salesforce védőfal rendszergazdai jelszó** szövegmező, írja be a fiókhoz tartozó jelszót.
 3. Az a **felhasználói biztonsági jogkivonatot** szövegmezőhöz illessze be a biztonsági token értékét.
 4. Kattintson a **ellenőrzése** a konfiguráció ellenőrzése.
 5. Kattintson a **következő** gombra kattintva nyissa meg a **megerősítő** lap.
9. Az a **megerősítő** kattintson **Complete** a konfiguráció mentéséhez.
   
## <a name="assigning-users"></a>Felhasználók hozzárendelése

A konfiguráció teszteléséhez kell biztosítania az Azure AD-felhasználók számára engedélyezni, használja az alkalmazás elérésére hozzárendelésével.

**Felhasználók hozzárendelése Salesforce védőfal, hajtsa végre az alábbi lépéseket:**

1. A klasszikus Azure portálon hozzon létre egy olyan fiókot.
2. Az a ** Salesforce védőfal ** alkalmazás integráció lapján, kattintson a **felhasználók hozzárendelése**.
   
   ![Felhasználók hozzárendelése](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "felhasználók hozzárendelése")
3. Adja meg a tesztfelhasználó számára, kattintson **hozzárendelése**, és kattintson a **Igen** a hozzárendelés megerősítéséhez.
   
   ![Igen](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Igen")

Most Várjon 10 percet, és ellenőrizze, hogy a szinkronizált fiókkal Salesforce védőfal felé.

Az SSO-beállítások tesztelésére, nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további tudnivalókért lásd: [a hozzáférési Panel bemutatása](https://msdn.microsoft.com/library/dn308586).

